# SkillSwap REST API Documentation
**Base URL:** `http://localhost:8000/api/`  
**Authentication Header:** `Authorization: Bearer <access_token>`

---

## 1. Authentication & Accounts API

### `POST /auth/register/`
- **Access:** Public
- **Description:** Multi-step registration. Creates user account, profile, wallet with 50 credits, and initial ledger bonus.
- **Request Body:**
```json
{
  "full_name": "Maya Krishnan",
  "username": "mayak",
  "email": "maya@student.edu",
  "password": "Password@123",
  "confirm_password": "Password@123",
  "college_name": "SkillSwap University",
  "department": "Computer Science",
  "year_of_study": "2nd Year",
  "learning_interests": ["Python Programming"],
  "wants_to_teach": true,
  "teaching_skills": ["Adobe Photoshop"],
  "teaching_experience_level": "intermediate"
}
```
- **Response (201 Created):**
```json
{
  "message": "Registration successful! 50 SkillSwap welcome credits have been deposited in your credit wallet.",
  "tokens": { "refresh": "...", "access": "..." },
  "user": { "id": 10, "username": "mayak", "wallet_balance": 50 }
}
```

### `POST /auth/login/`
- **Access:** Public
- **Request Body:**
```json
{
  "username": "arun",
  "password": "Password@123"
}
```
- **Response (200 OK):**
```json
{
  "tokens": { "refresh": "...", "access": "..." },
  "user": { "id": 1, "username": "arun", "role": "student", "wallet_balance": 50 }
}
```

### `GET /auth/me/`
- **Access:** Authenticated
- **Response (200 OK):** Current authenticated user details, profile completion score, wallet balance, and escrow credits.

---

## 2. Profiles API

### `GET /profiles/me/` & `PATCH /profiles/me/`
- **Access:** Authenticated
- **PATCH Request:** Update bio, department, learning goals, teaching mode.

### `GET /profiles/me/availabilities/` & `POST /profiles/me/availabilities/`
- **Access:** Authenticated
- **POST Request:** Update day of week (0-6) and start/end time.

---

## 3. Skills & Matching API

### `GET /skills/categories/`
- **Access:** Public
- **Response (200 OK):** List of all skill categories with display icons and skills count.

### `GET /skills/`
- **Access:** Public
- **Query Params:** `?category=programming`
- **Response (200 OK):** List of skills and standard curriculum topics.

### `GET /learning/tutors/`
- **Access:** Public / Authenticated
- **Query Params:** `?q=python&category=programming&level=intermediate&min_rating=4.5&max_credits=15&sort_by=rating`
- **Response (200 OK):** Filtered list of verified tutor cards with ratings, completed sessions, and credit cost.

### `GET /learning/recommendations/`
- **Access:** Authenticated
- **Description:** Intelligent Skill Matching engine (Section 11).
- **Response (200 OK):** Ranked tutor recommendations with `relevance_score` (0-100) and `match_explanation`.

---

## 4. Teaching & Verification API

### `GET /teaching/my-skills/` & `POST /teaching/my-skills/`
- **Access:** Authenticated
- **POST Request:**
```json
{
  "skill": 1,
  "experience_level": "intermediate",
  "experience_duration": "2 years",
  "description": "Hands-on Python tutor.",
  "topics_offered": ["Loops", "OOP", "Functions"]
}
```
- **Response (201 Created):** Sets `hourly_rate=15`, `verification_status="pending"`, `is_published=false`.

### `GET /verification/skills/{teaching_skill_id}/questions/`
- **Access:** Authenticated
- **Response (200 OK):** Dynamic 5-question assessment bank for the specific skill (MCQs and descriptive tasks).

### `POST /verification/skills/{teaching_skill_id}/submit/`
- **Access:** Authenticated
- **Request Body:**
```json
{
  "answers": [
    { "question_id": 1, "selected_option_id": "A" },
    { "question_id": 2, "descriptive_response": "Detailed explanation..." }
  ]
}
```
- **Response (200 OK):** Returns score percentage, feedback, and verification outcome (`verified`, `provisional`, `rejected`).

---

## 5. Learning Sessions & Escrow API

### `POST /learning/sessions/request/`
- **Access:** Authenticated (Learner)
- **Description:** Reserves credits in escrow atomically, validates tutor schedule, and creates session record.
- **Request Body:**
```json
{
  "tutor_id": 2,
  "skill_id": 1,
  "topics": ["Variables", "Loops"],
  "scheduled_date": "2026-11-22",
  "start_time": "16:00:00",
  "duration_minutes": 60,
  "session_mode": "online"
}
```

### `PATCH /teaching/requests/{id}/accept/` & `PATCH /teaching/requests/{id}/reject/`
- **Access:** Authenticated (Tutor)
- **Description:** Accept confirms schedule. Reject immediately refunds escrowed credits to the learner.

### `PATCH /sessions/{id}/complete/`
- **Access:** Authenticated
- **Description:** Mutual completion confirmation workflow. When both learner and tutor mark completed, releases escrow payout to tutor, increments hours and completed session metrics, and triggers achievement evaluation.

### `POST /sessions/{id}/cancel/`
- **Access:** Authenticated
- **Description:** Cancels scheduled session and refunds escrowed credits back to the learner.

### `POST /sessions/{id}/dispute/`
- **Access:** Authenticated
- **Description:** Reports a dispute to campus administration for formal arbitration before credit settlement.

---

## 6. Credit Wallet & Ledger API

### `GET /wallet/`
- **Access:** Authenticated
- **Response (200 OK):** Available balance, escrow balance, total earned, total spent.

### `GET /wallet/transactions/`
- **Access:** Authenticated
- **Query Params:** `?type=earned`, `?type=spent`, `?type=escrow`, `?type=refund`, `?type=bonus`
- **Response (200 OK):** Immutable ledger audit records with UUID, parties, type, previous and updated balance snapshots.

---

## 7. Skill Swap API

### `GET /swaps/mutual-matches/`
- **Access:** Authenticated
- **Response (200 OK):** Direct mutual barter matches discovered between students (0 credits).

### `POST /swaps/`
- **Access:** Authenticated
- **Request Body:**
```json
{
  "receiver_id": 2,
  "initiator_skill_offered_id": 1,
  "receiver_skill_requested_id": 7,
  "message": "Let us trade Python lessons for Photoshop lessons!"
}
```

### `PATCH /swaps/{id}/accept/` & `PATCH /swaps/{id}/complete/`
- **Access:** Authenticated
- **Description:** 0-credit mutual schedule coordination and completion confirmation.

---

## 8. AI Chatbot API

### `GET /chatbot/history/`
- **Access:** Authenticated
- **Response (200 OK):** Chat history between student and SkillSwap Assistant.

### `POST /chatbot/message/`
- **Access:** Authenticated
- **Request Body:** `{ "message": "How do credits work in escrow?" }`
- **Response (200 OK):** Detailed intelligent response from SkillSwap Assistant with live database context.

---

## 9. Campus Administration API

### `GET /admin/stats/`
- **Access:** Administrator (`is_platform_admin`)
- **Response (200 OK):** Platform KPIs, active accounts, completed sessions, credits settled, monthly analytics.

### `PATCH /admin/verifications/{id}/`
- **Access:** Administrator
- **Request Body:** `{ "action": "approve", "comments": "Passed manual review." }`

### `PATCH /admin/disputes/{id}/resolve/`
- **Access:** Administrator
- **Request Body:** `{ "resolution": "refund_learner", "admin_notes": "Tutor was absent." }`
- **Action:** Triggers atomic ledger update releasing escrow refund or payout.
