# SKILLSWAP: Data Structures & Logic Engine
> *Teach what you know. Learn what you want.*

- **Project:** SkillSwap - Student Peer-to-Peer Skill Exchange
- **Repository:** [github.com/chitraaralikatti-star/skillswap_engine](https://github.com/chitraaralikatti-star/skillswap_engine)
- **Folder:** `02_Data_Structures/`
- **Tech Stack:** Python, Django, Django REST Framework, React, JavaScript

---

## Quick Highlights
- **9 working modules** powering the application
- **Smart tutor scoring** evaluated out of 100
- **Zero-credit mutual skill swap** matching
- **Safe credit wallet** with atomic transaction escrow
- **50 free welcome credits** for every registered student

---

## 1. What Is This Engine?
SkillSwap allows students to teach what they already know and learn what they want to learn. For instance, if one student knows graphic design and wants to learn Python, while another student knows Python and wants design, SkillSwap finds that mutual match!

The engine acts as the computational brain of the application. It governs how data is searched, filtered, scored, ranked, and updated, ensuring SkillSwap operates as a reliable, fully functioning exchange platform rather than just a static interface.

---

## 2. The Problem
- Students possess valuable skills but lack an organized campus environment to share them.
- Finding matching learning partners manually is slow, cumbersome, and inefficient.
- Skill competencies need verification, sessions require structured lifecycle management, and credit balances must be handled fairly.

**Our Solution:** A unified platform integrating algorithmic matching and transaction safety.

---

## 3. How It Works (3 Layers)
1. **Frontend (React):** Handles user interaction, inputs, and displays real-time results.
2. **Backend (Django):** Executes business rules, validation, matching logic, and state transitions.
3. **Database (Django ORM):** Persists student profiles, skills, active sessions, and wallet ledgers.

**Workflow Pipeline:**
`Student` ➔ `React Page` ➔ `API Request` ➔ `Django Backend Logic` ➔ `Database ORM` ➔ `JSON Response` ➔ `Render on Screen`

---

## 4. The 9 Core Modules

### Module 1: Student & Profile (`accounts/models.py`)
- Stores student account details, college affiliation, user ratings, and day availability.
- Dynamic profile completion percentage computed via condition rules.

### Module 2: Skill Management (`skills/models.py`)
- Maintains catalog of teaching skills and learning interests.
- Automatic credit hourly rate tiers:
  - **Beginner:** 10 credits / hour
  - **Intermediate:** 15 credits / hour
  - **Advanced:** 20 credits / hour
- Only verified skills are published to the public search index.

### Module 3: Search & Filter (`skills/views.py`)
- Filters tutors by skill name, category, proficiency level, rating threshold, max credit rate, and teaching mode.
- Supports sorting by average rating, total completed sessions, hourly rate, or verification score.

### Module 4: Smart Tutor Matching (`skills/views.py`)
Every potential tutor is assigned a dynamic relevance score out of 100:
- **Verification Weight (40%):** `(verification_pct / 100) * 40`
- **Rating Weight (30%):** `(rating / 5.0) * 30`
- **Experience Weight (15%):** `(min(sessions_count, 15) / 15) * 15`
- **Availability Weight (15%):** `15` if scheduled days intersect, else `8`

*Example:* A tutor with 90% verification, 4.5 star rating, 10 sessions, and matching days:
$$\text{Score} = 36 + 27 + 10 + 15 = 88 / 100$$

Matching days are computed via mathematical set intersection:
```python
common_days = learner_days.intersection(tutor_days)
```
Tutors are ranked in descending order:
```python
matched_results.sort(key=lambda x: x['relevance_score'], reverse=True)
```
*(Note: Transparent, rule-based algorithmic scoring rather than black-box ML).*

### Module 5: Mutual Skill Swap (`swaps/views.py`)
- Detects bilateral trade matches (Student A teaches X and wants Y; Student B teaches Y and wants X).
- Direct swap sessions are executed without requiring credit deductions.

### Module 6: Verification & Assessment (`verification/views.py`)
- Administers objective assessment questions per skill area.
- Scores responses programmatically to award verification status. Certification alone does not bypass technical assessment.

### Module 7: Learning Sessions (`learning_sessions/views.py`)
- Enforces session lifecycles: `Requested` ➔ `Accepted / Rejected` ➔ `Completed`, `Cancelled`, or `Disputed`.
- Validations verify tutor existence, active skill verification status, absence of schedule collisions, and sufficient escrow credits. Minimum billing increment is 0.5 hours.

### Module 8: Credit Wallet & Escrow (`wallet/services.py`)
- 50 welcome credits credited upon student registration.
- Credits are locked in an escrow state upon session booking.
- On successful session completion, credits release to the tutor; on cancellation, credits refund to the learner.
- Employs `transaction.atomic()` and `select_for_update()` database row locking to prevent race conditions and double-spending.

### Module 9: Achievements & Milestones (`achievements/services.py`)
- Monitors verified skills and session thresholds.
- Idempotently grants badges and milestones, accompanied by automated notifications.

---

## 5. Frontend Pages
- **`DashboardPage.jsx`:** Consolidates wallet balances, upcoming sessions, recommended tutors, and recent achievements utilizing `Promise.all` parallel fetching and collection filters.
- **`LearnPage.jsx`:** Provides exploratory search, multi-attribute filtering, tutor cards, and instant session booking calculations.
- **`SkillSwapPage.jsx`:** Displays reciprocal swap suggestions and allows 1-click barter requests.

---

## 6. Data Structures & Algorithmic Operations
- **Lists & Dictionaries:** JSON payload mapping, API serialization, and in-memory aggregation.
- **Database QuerySets:** Filtered, indexed database lookups with conditional chaining.
- **Sets & Set Intersection:** Fast $O(1)$ overlap detection for weekly availability schedules (`learner_days & tutor_days`).
- **Sorting Algorithms:** Multi-key in-memory sorting for relevance ranking ($O(n \log n)$).
- **JavaScript Functional Primitives:** `map`, `filter`, and `slice` for non-destructive rendering transformations.

---

## 7. End-to-End Workflow
```
Student Registration
        │
        ▼
Profile Setup & Skill Addition
        │
        ▼
Verification Assessment Check
        │
        ▼
Tutor Search & Algorithmic Matching
        │
        ▼
Session Request Initiated
        │
        ▼
Wallet Balance Check & Escrow Lock
        │
        ▼
Tutor Accepts & Conducts Session
        │
        ▼
Session Completion Confirmed
        │
        ▼
Escrow Release / Tutor Payout
        │
        ▼
Milestones & Badges Awarded
```

---

## 8. Significance & Impact
- Eliminates manual search friction by matching partners algorithmically.
- Maintains high instructional standards through prerequisite skill assessments.
- Guarantees financial security with atomic credit escrow.
- Fosters continuous campus engagement through achievement gamification.

---

## 9. Future Scope
- Machine learning-based collaborative filtering for recommendation refinement.
- WebRTC-based video and interactive whiteboard integration.
- Native mobile clients with push notifications.
- Inter-college exchange leagues and competitive leaderboards.

---

## Conclusion
The SkillSwap Engine serves as the core logic foundation powering peer-to-peer student exchanges. By uniting search filtration, algorithmic relevance ranking, mathematical set intersection, and ACID-compliant credit handling, it transforms raw student listings into an active, secure educational community.
