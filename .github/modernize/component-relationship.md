# Component Relationship Diagram

## Component Overview

```mermaid
graph TB
    subgraph Presentation["Presentation Layer"]
        HC["HomeController\n────────────\n• GET / → index view\n• POST /upload → JSON response"]
        DC["DetailController\n────────────\n• GET /detail/{id} → detail view\n• POST /detail/{id}/delete → redirect"]
        PFC["PhotoFileController\n────────────\n• GET /photo/{id} → binary image data"]
        IDX["index.html (Thymeleaf)\n(Gallery grid + upload form)"]
        DET["detail.html (Thymeleaf)\n(Single photo + navigation)"]
        LAY["layout.html (Thymeleaf)\n(Base page layout)"]
        CSS["site.css\n(Styles)"]
        JS["upload.js\n(Drag-and-drop upload)"]
    end

    subgraph Domain["Domain / Model Layer"]
        Photo["Photo (JPA Entity)\n────────────\n• id: String (UUID)\n• originalFileName: String\n• photoData: byte[]\n• storedFileName: String\n• filePath: String\n• fileSize: Long\n• mimeType: String\n• uploadedAt: LocalDateTime\n• width: Integer\n• height: Integer"]
        UploadResult["UploadResult\n────────────\n• success: boolean\n• fileName: String\n• errorMessage: String\n• photoId: String"]
        MathUtil["MathUtil\n────────────\n• gcd(int, int): int\n(utility; not used in core flow)"]
    end

    subgraph Service["Service Layer"]
        PhotoSvc["PhotoService (Interface)\n────────────\n• getAllPhotos()\n• getPhotoById(id)\n• uploadPhoto(file)\n• deletePhoto(id)\n• getPreviousPhoto(photo)\n• getNextPhoto(photo)"]
        PhotoSvcImpl["PhotoServiceImpl\n────────────\n• Validates file type & size\n• Reads binary data from upload\n• Extracts image dimensions (ImageIO)\n• Generates UUID-based filename\n• Saves to Oracle DB via repository\n• Deletes from Oracle DB"]
    end

    subgraph Repository["Repository Layer"]
        PhotoRepo["PhotoRepository\n(JpaRepository<Photo, String>)\n────────────\n• findAllOrderByUploadedAtDesc()\n• findPhotosUploadedBefore(ts)\n• findPhotosUploadedAfter(ts)\n• findPhotosByUploadMonth(y, m)\n• findPhotosWithPagination(s, e)\n• findPhotosWithStatistics()"]
    end

    subgraph Database["Data Layer"]
        OraDB[("Oracle Database\nFree 23ai\n────────────\nTable: PHOTOS\n• ID (PK, VARCHAR)\n• ORIGINAL_FILE_NAME\n• PHOTO_DATA (BLOB)\n• STORED_FILE_NAME\n• FILE_PATH\n• FILE_SIZE\n• MIME_TYPE\n• UPLOADED_AT\n• WIDTH\n• HEIGHT")]
    end

    HC -->|uses| PhotoSvc
    DC -->|uses| PhotoSvc
    PFC -->|uses| PhotoSvc
    HC -->|renders| IDX
    DC -->|renders| DET
    IDX -->|extends| LAY
    DET -->|extends| LAY
    IDX -->|loads| CSS
    IDX -->|loads| JS

    PhotoSvc -->|implements| PhotoSvcImpl
    PhotoSvcImpl -->|uses| PhotoRepo
    PhotoSvcImpl -->|creates/returns| Photo
    PhotoSvcImpl -->|creates/returns| UploadResult

    PhotoRepo -->|manages| Photo
    PhotoRepo -->|queries| OraDB
    Photo -->|maps to| OraDB
```

## Request-Response Flow

```mermaid
sequenceDiagram
    actor User as User (Browser)
    participant HC as HomeController
    participant PS as PhotoServiceImpl
    participant PR as PhotoRepository
    participant DB as Oracle Database

    User->>HC: GET /
    HC->>PS: getAllPhotos()
    PS->>PR: findAllOrderByUploadedAtDesc()
    PR->>DB: SELECT ... FROM PHOTOS ORDER BY UPLOADED_AT DESC
    DB-->>PR: List<Photo>
    PR-->>PS: List<Photo>
    PS-->>HC: List<Photo>
    HC-->>User: Render index.html (photo gallery)

    User->>HC: POST /upload (multipart files)
    HC->>PS: uploadPhoto(file) [for each file]
    PS->>PS: Validate MIME type & size
    PS->>PS: Read bytes, extract dimensions (ImageIO)
    PS->>PR: save(photo)
    PR->>DB: INSERT INTO PHOTOS (with BLOB data)
    DB-->>PR: Saved Photo
    PR-->>PS: Photo
    PS-->>HC: UploadResult (success, photoId)
    HC-->>User: JSON response (uploaded photos list)

    User->>+PFC: GET /photo/{id}
    PFC->>PS: getPhotoById(id)
    PS->>PR: findById(id)
    PR->>DB: SELECT ... FROM PHOTOS WHERE ID = ?
    DB-->>PR: Photo (with BLOB data)
    PR-->>PS: Optional<Photo>
    PS-->>PFC: Optional<Photo>
    PFC-->>-User: HTTP 200 (binary image data with Content-Type)
```

## Component Coupling Analysis

| Component | Depends On | Coupling Type | Risk |
|-----------|-----------|---------------|------|
| HomeController | PhotoService, Photo, UploadResult | Interface (loose) | Low |
| DetailController | PhotoService, Photo | Interface (loose) | Low |
| PhotoFileController | PhotoService, Photo | Interface (loose) | Low |
| PhotoServiceImpl | PhotoRepository, Photo, UploadResult, ImageIO | Direct | Medium |
| PhotoRepository | Photo, Oracle DB (native SQL) | Oracle-tight | High |
| Photo (Entity) | Oracle DB (OracleDialect, BLOB) | Oracle-tight | High |
| application.properties | Oracle JDBC URL, credentials | Hard-coded | Critical |
