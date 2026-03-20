# Research Findings: Document Upload and Management Feature

**Feature**: Document Upload and Management  
**Date**: 2026-03-20  
**Context**: .NET 8.0 Blazor Server application with local file storage

## Windows Defender API Integration for File Scanning

**Decision**: Use Windows Defender command-line tool (MpCmdRun.exe) invoked asynchronously via background job for virus scanning  
**Rationale**: Windows Defender lacks a direct .NET API for file scanning. Command-line approach provides reliable scanning without external dependencies. Asynchronous processing prevents UI blocking during uploads. For cloud migration, Azure Functions with Queue Storage triggers can handle scanning post-upload.  
**Alternatives Considered**: 
- ClamAV open-source scanner (requires installation, cross-platform but adds complexity)
- Third-party cloud services (introduces external dependencies, not suitable for offline training)
- Mock scanning (not realistic for training)

**Implementation Approach**:
- Upload file to temporary location
- Queue scan job with file path
- Return success to user immediately
- Background job processes scan and updates document status
- Notify user of scan results via UI

## Secure File Upload and Storage in ASP.NET Core

**Decision**: Use ASP.NET Core IFormFile with server-side validation, store files outside wwwroot with GUID-based filenames  
**Rationale**: Follows Microsoft security guidelines. Storing outside wwwroot prevents direct web access, GUID filenames prevent path traversal attacks. Server-side validation ensures client-side bypass doesn't compromise security.  
**Alternatives Considered**:
- Client-side only validation (insufficient security)
- Database blob storage (overkill for local training, adds complexity)
- User-supplied filenames (security risk)

## Role-Based File Access Control in Blazor Server

**Decision**: Implement authorization at service layer with ownership checks and role validation  
**Rationale**: Blazor Server runs on server, so service-layer checks prevent unauthorized access. Combine with [Authorize] attributes for UI-level protection. Check user ownership and project membership for access control.  
**Alternatives Considered**:
- UI-only authorization (easily bypassed)
- Database-level row security (complex for multi-tenant scenarios)
- Custom middleware (unnecessary complexity)

## File Upload Progress Indication in Blazor

**Decision**: Use Blazor InputFile component with custom progress reporting via JavaScript interop  
**Rationale**: InputFile provides built-in streaming upload. JavaScript interop allows progress callbacks during upload. Maintains server-side processing while providing user feedback.  
**Alternatives Considered**:
- Custom JavaScript upload (loses Blazor integration)
- Server-sent events (overkill for simple progress)
- No progress indication (poor UX)

## Document Search Implementation in .NET with EF Core

**Decision**: Implement search using EF Core LIKE queries with full-text indexing on title, description, and tags  
**Rationale**: EF Core supports efficient LIKE queries. Full-text search provides good performance for moderate datasets. Can be extended with SQL Server full-text search if needed.  
**Alternatives Considered**:
- Elasticsearch (external dependency, overkill for training)
- In-memory search (doesn't scale)
- No search functionality (incomplete feature)