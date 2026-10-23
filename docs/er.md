erDiagram
    STUDENT ||--o{ APPLICATION : submits
    INTERNSHIP ||--o{ APPLICATION : receives
    COMPANY ||--o{ INTERNSHIP : publishes

    STUDENT {
        int student_id PK
        string full_name
        string email
        string department
    }

    COMPANY {
        int company_id PK
        string company_name
        string industry
    }

    INTERNSHIP {
        int internship_id PK
        int company_id FK
        string title
        string description
        date application_deadline
    }

    APPLICATION {
        int application_id PK
        int student_id FK
        int internship_id FK
        date application_date
        string status
    }
