# Data Model: Document Upload and Management Feature

**Feature**: Document Upload and Management  
**Date**: 2026-03-20  
**Context**: Extends existing ContosoDashboard data model with document management entities

## Entities

### Document

Represents an uploaded file with metadata and access control.

**Attributes**:
- `Id` (int, PK, auto-increment)
- `Title` (string, required, max 255 chars) - User-provided document title
- `Description` (string, optional, max 1000 chars) - Optional description
- `Category` (string, required, enum: "Project Documents", "Team Resources", "Personal Files", "Reports", "Presentations", "Other")
- `ProjectId` (int?, optional, FK to Project) - Associated project if applicable
- `Tags` (string, optional, max 500 chars) - Comma-separated tags for search
- `FilePath` (string, required, max 500 chars) - Unique file path on disk
- `FileSize` (long, required) - Size in bytes
- `MimeType` (string, required, max 255 chars) - MIME type (e.g., "application/pdf")
- `UploadDate` (DateTime, required) - When uploaded
- `UploadedById` (string, required, FK to User) - User who uploaded
- `ScanStatus` (string, required, enum: "Pending", "Clean", "Infected", "Failed") - Virus scan result
- `ScanDate` (DateTime?, optional) - When scan completed

**Relationships**:
- `UploadedBy` (User, many-to-one) - The user who uploaded the document
- `Project` (Project, many-to-one, optional) - Associated project

**Validation Rules**:
- Title: Required, not empty, max 255 characters
- Category: Must be one of predefined values
- FileSize: Must be <= 25,000,000 bytes (25MB)
- MimeType: Must be in allowed list (PDF, Office docs, text, images)
- FilePath: Must be unique, follow pattern `{userId}/{projectId or "personal"}/{guid}.{ext}`

**Business Rules**:
- Only document owner can delete
- Project members can view project documents
- Documents with "Infected" scan status cannot be downloaded
- Documents with "Pending" scan status show processing indicator
- Scan must complete before document is fully available
- Administrators have full access

### DocumentCategory (Enum)

Predefined categories for document organization.

**Values**:
- ProjectDocuments
- TeamResources
- PersonalFiles
- Reports
- Presentations
- Other

## Data Flow

1. **Upload**: User selects file → Validate → Generate unique path → Save to disk → Set status to "Pending" → Queue scan job → Save metadata to DB → Return success
2. **Scan**: Background job scans file → Update status to "Clean"/"Infected"/"Failed" → Set ScanDate
3. **View**: Query documents → Check permissions and scan status → Return metadata (block infected files)
4. **Download**: Check permissions and scan status → Stream file from disk
5. **Delete**: Check ownership → Delete from disk → Remove from DB

## Database Schema Changes

```sql
-- Add to existing ApplicationDbContext

public DbSet<Document> Documents { get; set; }

modelBuilder.Entity<Document>(entity =>
{
    entity.HasKey(e => e.Id);
    entity.Property(e => e.Title).IsRequired().HasMaxLength(255);
    entity.Property(e => e.Description).HasMaxLength(1000);
    entity.Property(e => e.Category).IsRequired();
    entity.Property(e => e.Tags).HasMaxLength(500);
    entity.Property(e => e.FilePath).IsRequired().HasMaxLength(500);
    entity.Property(e => e.MimeType).IsRequired().HasMaxLength(255);
    
    entity.HasOne(d => d.UploadedBy)
        .WithMany(u => u.Documents) -- Add navigation property to User
        .HasForeignKey(d => d.UploadedById)
        .OnDelete(DeleteBehavior.Restrict);
        
    entity.HasOne(d => d.Project)
        .WithMany(p => p.Documents) -- Add navigation property to Project
        .HasForeignKey(d => d.ProjectId)
        .OnDelete(DeleteBehavior.SetNull);
});
```

## Migration Notes

- Add Documents table with above schema
- Add navigation properties to User and Project models
- Ensure unique constraint on FilePath
- Consider adding indexes on UploadDate, Category, ProjectId for query performance