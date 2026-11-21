# SkillSwap System Diagrams & Visual Models
**Project Theme:** Theme 3 – Ledger and Money Systems  
**Project:** SkillSwap – Student Skill Exchange and Credit Ledger System

---

## 1. System Architecture Diagram

```mermaid
flowchart TD
    subgraph ClientLayer ["Client Layer (Browser & Mobile Responsive)"]
        UI["React.js + Tailwind CSS UI"]
        State["Context State (Auth, Theme, Chatbot)"]
        Axios["Axios REST Client + JWT Interceptors"]
    end

    subgraph APIGateway ["API & Middleware Gateway"]
        CORS["Django CORS Headers"]
        JWTAuth["Simple JWT Authentication"]
        Perms["Role-Based Permission Classes"]
    end

    subgraph ServiceLayer ["Django Application Services"]
        AccService["Accounts & Student Profiles"]
        SkillService["Skill Catalog & Matching Engine"]
        VerifService["Dynamic Assessment & Verification"]
        WalletService["Atomic Credit Wallet & Ledger Service"]
        SessionService["Session Lifecycle & Mutual Escrow"]
        SwapService["1:1 Skill Swap Barter Engine"]
        MatService["Learning Materials Repository"]
        RevService["Multi-Criteria Reviews"]
        NotifService["In-App Notifications"]
        AchieveService["Backend Gamification Badges"]
        AIService["SkillSwap Assistant (LLM + Rule Engine)"]
        AdminService["Admin Oversight & Dispute Arbiter"]
    end

    subgraph PersistenceLayer ["Data & Storage Layer"]
        PostgresDB[(PostgreSQL / Relational Database)]
        LedgerTable[("Immutable Credit Ledger Table\n(select_for_update, atomic)")]
        MediaStore["Media Files (Certificates, PDFs, Materials)"]
    end

    UI --> State
    State --> Axios
    Axios -->|JSON over HTTP| CORS
    CORS --> JWTAuth
    JWTAuth --> Perms
    Perms --> ServiceLayer

    WalletService -->|Atomic Transaction Lock| LedgerTable
    ServiceLayer -->|Django ORM| PostgresDB
    MatService -->|Validated File Storage| MediaStore
```

---

## 2. Entity-Relationship (ER) Diagram

```mermaid
erDiagram
    USER ||--o| STUDENT_PROFILE : has
    USER ||--o| CREDIT_WALLET : owns
    USER ||--o{ AVAILABILITY : sets
    USER ||--o{ STUDENT_TEACHING_SKILL : registers
    USER ||--o{ STUDENT_LEARNING_INTEREST : expresses
    USER ||--o{ CREDIT_TRANSACTION : triggers
    USER ||--o{ NOTIFICATION : receives
    USER ||--o{ STUDENT_ACHIEVEMENT : earns

    SKILL_CATEGORY ||--o{ SKILL : classifies
    SKILL ||--o{ STUDENT_TEACHING_SKILL : categorized_in
    SKILL ||--o{ STUDENT_LEARNING_INTEREST : targeted_by
    SKILL ||--o{ ASSESSMENT_QUESTION : has_bank

    STUDENT_TEACHING_SKILL ||--o{ SKILL_ASSESSMENT : tested_via
    SKILL_ASSESSMENT ||--o{ ASSESSMENT_ANSWER : contains
    STUDENT_TEACHING_SKILL ||--o| SKILL_VERIFICATION : certified_by

    USER ||--o{ LEARNING_SESSION : teaches
    USER ||--o{ LEARNING_SESSION : learns
    LEARNING_SESSION ||--o| CREDIT_TRANSACTION : escrow_hold
    LEARNING_SESSION ||--o| REVIEW : reviewed_in
    LEARNING_SESSION ||--o| DISPUTE : disputed_via
    LEARNING_SESSION ||--o{ LEARNING_MATERIAL : shares

    USER ||--o{ SKILL_SWAP_REQUEST : initiates
    USER ||--o{ SKILL_SWAP_REQUEST : receives

    ACHIEVEMENT ||--o{ STUDENT_ACHIEVEMENT : granted_to

    USER {
        int id PK
        string username UK
        string email UK
        string role
        boolean is_active
        datetime created_at
    }

    STUDENT_PROFILE {
        int id PK
        int user_id FK
        string college_name
        string department
        string year_of_study
        decimal total_teaching_hours
        decimal total_learning_hours
        decimal average_rating
        int completed_sessions
    }

    CREDIT_WALLET {
        int id PK
        int user_id FK
        int available_balance
        int escrow_balance
        int total_earned
        int total_spent
    }

    CREDIT_TRANSACTION {
        uuid transaction_id PK
        int sender_id FK
        int receiver_id FK
        int session_id
        string transaction_type
        int amount
        int sender_previous_balance
        int sender_updated_balance
        int receiver_previous_balance
        int receiver_updated_balance
        string status
        datetime timestamp
    }

    LEARNING_SESSION {
        int id PK
        int teacher_id FK
        int learner_id FK
        int skill_id FK
        date scheduled_date
        time start_time
        int duration_minutes
        int credit_amount
        string status
        boolean teacher_completed_confirmed
        boolean learner_completed_confirmed
    }

    SKILL_SWAP_REQUEST {
        int id PK
        int initiator_id FK
        int receiver_id FK
        int initiator_skill_offered_id FK
        int receiver_skill_requested_id FK
        string status
        boolean initiator_confirmed_completed
        boolean receiver_confirmed_completed
    }
```

---

## 3. Use Case Diagram

```mermaid
flowchart LR
    Student((Student))
    Admin((Administrator))

    subgraph SkillSwap_System ["SkillSwap Platform Boundary"]
        UC1["Register (Claim 50 Credits)"]
        UC2["Login & Manage Profile"]
        UC3["Register Teaching Skill"]
        UC4["Complete Dynamic Assessment Quiz"]
        UC5["Search & Filter Verified Tutors"]
        UC6["Request Learning Session"]
        UC7["Hold Escrow Credits"]
        UC8["Mutually Confirm Session Completion"]
        UC9["Release Payout to Teacher"]
        UC10["Initiate 1:1 Skill Swap (0 Credits)"]
        UC11["Access / Upload Learning Materials"]
        UC12["Submit Multi-Criteria Tutor Review"]
        UC13["Consult SkillSwap AI Assistant"]
        UC14["Review Pending Verifications"]
        UC15["Arbitrate Session Disputes"]
        UC16["Audit Immutable Transaction Ledger"]
        UC17["Manage Student Account Status"]
    end

    Student --> UC1
    Student --> UC2
    Student --> UC3
    Student --> UC4
    Student --> UC5
    Student --> UC6
    Student --> UC8
    Student --> UC10
    Student --> UC11
    Student --> UC12
    Student --> UC13

    UC6 -.->|triggers| UC7
    UC8 -.->|triggers| UC9

    Admin --> UC14
    Admin --> UC15
    Admin --> UC16
    Admin --> UC17
```

---

## 4. Data Flow Diagram (DFD) Level 0 – Context Diagram

```mermaid
flowchart TD
    Student["Student (Teacher / Learner)"]
    Admin["Campus Administrator"]
    System[["SkillSwap Platform\n(Theme 3: Ledger & Money System)"]]

    Student -->|Registration info, teaching skills, session requests, reviews| System
    System -->|50 welcome credits, verified tutor listings, session reminders, notifications| Student

    Admin -->|Verification approvals, dispute resolutions, student status toggles| System
    System -->|Platform KPIs, pending skill submissions, dispute cases, audit logs| Admin
```

---

## 5. Data Flow Diagram (DFD) Level 1 – Operational Processes

```mermaid
flowchart TD
    S[Student] --> P1[1.0 Authentication & Wallet Setup]
    P1 --> D1[(User & Wallet Store)]
    D1 -->|50 Welcome Credits| S

    S --> P2[2.0 Skill Registration & Verification]
    P2 --> D2[(Skills & Question Bank)]
    D2 -->|Dynamic Assessment Quiz| P2
    P2 -->|Score >= 80%| D3[(Verified Tutors Store)]

    S --> P3[3.0 Search & Intelligent Matching]
    D3 --> P3
    P3 -->|Ranked Tutor Cards & Explanations| S

    S --> P4[4.0 Session Booking & Escrow Locking]
    P4 -->|Lock Available Credits| D4[(Escrow Ledger)]

    S --> P5[5.0 Mutual Completion & Settlement]
    D4 --> P5
    P5 -->|Release Payout to Teacher| D1

    S --> P6[6.0 Direct Skill Swap Barter]
    D3 --> P6
    P6 -->|Zero-Credit Matching| S

    A[Admin] --> P7[7.0 Administrative Dispute Arbitrations]
    P7 -->|Refund Learner or Payout Teacher| D4
```

---

## 6. Data Flow Diagram (DFD) Level 2 – Credit Ledger & Escrow Settlement

```mermaid
sequenceDiagram
    autonumber
    actor Learner as Student Learner
    actor Teacher as Student Teacher
    participant System as Learning Session Controller
    participant Wallet as Wallet Service (atomic)
    participant Ledger as Immutable Transaction Ledger
    actor Admin as Campus Admin

    Note over Learner,Teacher: Session Booking Phase
    Learner->>System: Request Session (e.g. 1 hr Python = 15 credits)
    System->>Wallet: Validate Available Balance >= 15
    Wallet->>Wallet: select_for_update() Lock Learner Wallet
    Wallet->>Wallet: available_balance -= 15, escrow_balance += 15
    Wallet->>Ledger: Insert 'session_escrow' transaction (status=pending)
    System->>Teacher: Notify: New Session Request Pending

    Note over Learner,Teacher: Execution & Mutual Confirmation Phase
    Teacher->>System: Accept Request & Conduct Session
    Learner->>System: Click "Mark as Completed"
    System->>System: learner_completed_confirmed = true
    Teacher->>System: Click "Mark as Completed"
    System->>System: teacher_completed_confirmed = true

    Note over Wallet,Ledger: Atomic Settlement Phase
    System->>Wallet: release_session_payout(learner, teacher, 15)
    Wallet->>Wallet: Lock both wallets in deterministic order
    Wallet->>Wallet: Learner: escrow_balance -= 15, total_spent += 15
    Wallet->>Wallet: Teacher: available_balance += 15, total_earned += 15
    Wallet->>Ledger: Update escrow transaction status = completed
    Wallet->>Ledger: Insert 'session_payout' immutable ledger entry
    System->>Learner: Prompt to submit 4-criteria tutor review
    System->>Teacher: Notify: 15 Credits credited to your wallet

    Note over Admin,Ledger: In Case of Dispute
    opt Dispute Raised
        Learner->>System: Raise Session Dispute (e.g. Tutor Absent)
        Admin->>System: Review evidence & select 'refund_learner'
        System->>Wallet: refund_session_escrow(learner, 15)
        Wallet->>Wallet: Learner escrow_balance -= 15, available_balance += 15
        Wallet->>Ledger: Insert 'session_refund' ledger entry
    end
```
