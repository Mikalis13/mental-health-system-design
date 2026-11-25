# Diagram source (Mermaid)

GitHub renders these diagrams directly. The same code is in the `diagrams/` folder as `.mmd` files.

## Figure 1 - Patient application workflow

```mermaid
flowchart TD
    A([Start]) --> B[Patient opens app]
    B --> C[Login with MFA]
    C --> D{Authenticated?}
    D -- No --> E[Show error and recovery option]
    E --> C
    D -- Yes --> F[Home dashboard]
    F --> G[View healthcare record]
    G --> H{Record incorrect?}
    H -- Yes --> I[Submit correction request]
    H -- No --> F
    F --> J[Book appointment]
    J --> K[Choose team or professional]
    K --> L[Select slot within 2 months]
    L --> M[Confirm appointment]
    F --> N[Complete exercise]
    N --> O[Record completion and mood score]
    F --> P[Submit digital form]
    P --> Q[Validate required answers]
    F --> R[Send secure message]
    R --> S[Attach text/audio/image if needed]
    I --> T[Server saves request and writes audit log]
    M --> T
    O --> T
    Q --> T
    S --> T
    T --> U[Return confirmation]
    U --> F
```

## Figure 2 - Medical professional application workflow

```mermaid
flowchart TD
    A([Start]) --> B[Professional opens dashboard]
    B --> C[Login with MFA]
    C --> D{Role authorised?}
    D -- No --> E[Access denied and audit event]
    D -- Yes --> F[Search patient]
    F --> G[Open patient dashboard]
    G --> H[View record summary]
    G --> I[Review engagement graphs]
    G --> J[Review alerts and form scores]
    H --> K[Add healthcare note]
    K --> L[Autocomplete drug/condition terms]
    L --> M[Save note as locked record]
    G --> N[Create or update exercise plan]
    N --> O[Send exercise update message]
    G --> P[Schedule appointment]
    P --> Q{Single/repeat/emergency?}
    Q --> R[Validate availability and duration]
    R --> S[Confirm appointment]
    M --> T[Server writes audit log]
    O --> T
    S --> T
    T --> U[Dashboard updated]
```

## Figure 3 - Server-side workflow

```mermaid
flowchart TD
    A([Request received]) --> B[Check TLS and session token]
    B --> C{Authenticated?}
    C -- No --> D[Return 401 and log failed access]
    C -- Yes --> E[Authorise role and patient relationship]
    E --> F{Authorised?}
    F -- No --> G[Return 403 and write audit log]
    F -- Yes --> H[Validate and sanitise input]
    H --> I{Valid input?}
    I -- No --> J[Return validation error]
    I -- Yes --> K{Request type}
    K -- Read --> L[Retrieve database/file data]
    K -- Write --> M[Store data in database/file store]
    K -- Update --> N[Create new version or correction request]
    L --> O[Write audit event if sensitive access]
    M --> O
    N --> O
    O --> P[Return response]
    D --> Q([End])
    G --> Q
    J --> Q
    P --> Q
```

## Figure 4 - Patient application dataflow

```mermaid
flowchart LR
    Patient[External Entity: Patient] --> P1[Process: Login and session management]
    P1 --> Auth[Process: Authentication service]
    Auth --> P1
    Patient --> P2[Process: View records and appointments]
    P2 --> API[Process: Backend API]
    API --> D1[(Data Store: Patient records database)]
    API --> D2[(Data Store: Appointment database)]
    D1 --> API
    D2 --> API
    API --> P2
    Patient --> P3[Process: Submit form/exercise/message]
    P3 --> API
    API --> D3[(Data Store: Engagement and forms)]
    API --> D4[(Data Store: Message store)]
    API --> D5[(Data Store: Audit log)]
    API --> Notify[Process: Notification service]
```

## Figure 5 - Medical professional application dataflow

```mermaid
flowchart LR
    Staff[External Entity: Medical professional] --> P1[Process: Staff login]
    P1 --> Auth[Process: Authentication service]
    Auth --> P1
    Staff --> P2[Process: Search and open patient dashboard]
    P2 --> API[Process: Backend API]
    API --> D1[(Data Store: Patient database)]
    API --> D2[(Data Store: Healthcare record database)]
    API --> D3[(Data Store: Engagement/form database)]
    D1 --> API
    D2 --> API
    D3 --> API
    API --> P2
    Staff --> P3[Process: Add note/update exercise/book appointment]
    P3 --> API
    API --> D4[(Data Store: Appointments)]
    API --> D5[(Data Store: Exercise plans)]
    API --> D6[(Data Store: File storage)]
    API --> D7[(Data Store: Audit log)]
    API --> Notify[Process: Notification service]
```

## Figure 6 - Server-side dataflow

```mermaid
flowchart TD
    Client[External Entity: Patient/professional app] --> API[Process: Backend API gateway]
    API --> Auth[Process: Authentication and RBAC]
    Auth --> API
    API --> Val[Process: Validation and sanitisation]
    Val --> Rec[Process: Records service]
    Val --> Appt[Process: Appointment service]
    Val --> Ex[Process: Exercise/form service]
    Val --> Msg[Process: Messaging service]
    Rec --> DB[(Data Store: Relational database)]
    Appt --> DB
    Ex --> DB
    Msg --> DB
    Rec --> Files[(Data Store: Secure file storage)]
    Msg --> Files
    Rec --> Audit[(Data Store: Immutable audit log)]
    Appt --> Audit
    Ex --> Audit
    Msg --> Audit
    Appt --> Notify[Process: Notification service]
    Ex --> Notify
    Msg --> Notify
    API --> Client
```

## Figure 7 - Overall system dataflow

```mermaid
flowchart LR
    Patient[External Entity: Patient app] --> API[Process: Backend API server]
    Professional[External Entity: Professional dashboard] --> API
    API --> Auth[Process: Authentication service]
    API --> DB[(Data Store: Secure database)]
    API --> Files[(Data Store: File storage for PDFs/images/audio)]
    API --> Audit[(Data Store: Audit logs)]
    API --> Notify[Process: Notification service]
    Notify --> Patient
    Notify --> Professional
    DB --> API
    Files --> API
    Auth --> API
    API --> Patient
    API --> Professional
```

## Figure 8 - Server-side ERD

```mermaid
erDiagram
    PATIENT ||--o{ APPOINTMENT : books
    MEDICALPROFESSIONAL ||--o{ APPOINTMENT : manages
    PATIENT ||--o{ HEALTHCARERECORD : has
    MEDICALPROFESSIONAL ||--o{ HEALTHCARERECORD : authors
    HEALTHCARERECORD ||--o{ RECORDATTACHMENT : includes
    PATIENT ||--o{ EXERCISEPLAN : receives
    MEDICALPROFESSIONAL ||--o{ EXERCISEPLAN : creates
    EXERCISEPLAN ||--o{ EXERCISEENGAGEMENT : records
    PATIENT ||--o{ MESSAGE : relates_to
    PATIENT ||--o{ DIGITALFORMRESPONSE : submits
    PATIENT ||--o{ AUDITLOG : target_patient
    MEDICALPROFESSIONAL ||--o{ AUDITLOG : actor
    PATIENT { string patient_id PK string nhs_number string first_name string last_name date date_of_birth string email string phone text accessibility_needs }
    MEDICALPROFESSIONAL { string professional_id PK string name string role string staff_number string email string department }
    APPOINTMENT { string appointment_id PK string patient_id FK string professional_id FK string appointment_type datetime start_datetime datetime end_datetime string status boolean emergency_flag }
    HEALTHCARERECORD { string record_id PK string patient_id FK string author_id FK text note_text datetime created_at string record_status boolean correction_requested }
    RECORDATTACHMENT { string attachment_id PK string record_id FK string file_type decimal file_size_mb string storage_uri text caption_note }
    EXERCISEPLAN { string plan_id PK string patient_id FK string professional_id FK string exercise_name text instructions date start_date date end_date string reminder_frequency }
    EXERCISEENGAGEMENT { string engagement_id PK string plan_id FK string patient_id FK datetime completed_at boolean completion_status int mood_score text notes }
    MESSAGE { string message_id PK string sender_id string recipient_id string patient_id FK text body_text string attachment_uri datetime sent_at datetime read_at }
    DIGITALFORMRESPONSE { string response_id PK string patient_id FK string form_type datetime submitted_at json response_json int calculated_score string risk_level }
    AUDITLOG { string audit_id PK string actor_id string action string target_entity string target_id datetime timestamp string ip_address text reason }
```
