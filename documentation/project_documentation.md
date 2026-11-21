# SkillSwap – Student Skill Exchange and Credit Ledger System
**College Mini Project Comprehensive Technical Report**  
**Theme:** Theme 3 – Ledger and Money Systems  
**Status:** Approved for Technical Evaluation and Demonstration

---

## 1. Project Abstract
SkillSwap is an enterprise-grade full-stack student skill exchange platform built to foster collaborative, peer-to-peer knowledge sharing across college campuses. Rather than relying on commercial monetary payments, the system establishes a self-sustaining internal **Credit Ledger Economy**. Students receive an initial grant of 50 credits upon registration, earn additional credits by teaching skills they have mastered, spend credits to learn from verified peers, or engage in 1:1 direct skill swaps with zero credits charged. 

To guarantee high instructional quality, tutors must pass dynamic, category-specific assessment quizzes before their teaching profiles can be published. All credit movements are governed by ACID-compliant atomic database transactions with row-level locking (`select_for_update()`), holding credits in a transparent escrow until mutual session completion is verified by both participants. An embedded SkillSwap Assistant powered by LLM integration and real-time database contextualization assists students in navigating platform workflows.

---

## 2. Problem Statement
In higher education environments, thousands of students possess valuable proficiencies in programming, web engineering, graphic design, data science, language fluency, and creative arts. However:
1. **Lack of Organized Campus Structure:** Students have no centralized platform to identify capable peer tutors or verify their subject matter knowledge.
2. **Financial Barriers:** Commercial learning platforms require recurring monetary subscriptions or high hourly fees that student budgets cannot support.
3. **No Contribution Incentives:** Talented students have little formal motivation to mentor peers when their time and effort are uncompensated.
4. **Session Uncertainty:** Informal tutoring often suffers from last-minute cancellations, schedule clashes, and ambiguous expectations.

---

## 3. Existing System vs. Proposed System

| Dimension | Existing Commercial Systems (Coursera, Udemy, Chegg) | Informal Campus Tutoring (WhatsApp / Word of Mouth) | Proposed SkillSwap System |
|---|---|---|---|
| **Payment Mechanism** | Real currency / Credit cards | Unpaid favors or cash | **Internal Credit Ledger System** |
| **Initial Barrier** | High paywall | Informal / Unreliable | **50 Free Credits on registration** |
| **Quality Verification** | Rigid degrees or automated certificates | Zero verification | **Dynamic, category-specific assessment quizzes** |
| **Security & Fairness** | Refund disputes take days | Frequent cancellations / no recourse | **Automated Escrow Hold & Mutual Confirmation** |
| **Barter Opportunities** | None | Ad-hoc, hard to match | **Direct Skill Swap Engine (0 Credits)** |
| **Gamification & Audit** | Basic completion percentages | None | **Verifiable Badges & Immutable Ledger Audit** |

---

## 4. Project Objectives
- Establish an organized, trusted peer learning ecosystem across university departments.
- Implement an ACID-compliant credit ledger system guaranteeing double-entry balance consistency and preventing race conditions or double spending.
- Enforce automated quality assurance through dynamic assessment quizzes (minimum 80% passing grade for verified tutor badges).
- Enable direct 1:1 mutual skill swaps requiring zero credit transfers when complementary knowledge is shared.
- Provide comprehensive student dashboards, analytical charts, learning resource management, multi-criteria reviews, and administrative dispute resolution.

---

## 5. Scope of the System
- **User Personas:** Students (acting interchangeably as Teachers and Learners) and Campus Administrators.
- **Academic Disciplines:** Technology, Web Development, Creative Arts, UI/UX Design, Data Science, Artificial Intelligence, Foreign Languages, and Professional Communication.
- **Financial Boundary:** Purely closed-loop campus credit economy with no real fiat currency transactions, ensuring zero liability and 100% student accessibility.

---

## 6. Functional Requirements
1. **Multi-Step Authentication:** 5-step onboarding collecting personal, learning, and teaching preferences, hashing passwords securely, and issuing JWT tokens.
2. **Wallet & Ledger:** Initial 50 credit bonus, atomic escrow hold on session booking, deterministic payout release on completion, and transaction history filtering.
3. **Skill Verification Engine:** Dynamic question bank generation for technical and non-technical skills, automated percentage scoring, and provisional review routing.
4. **Intelligent Matchmaking:** Algorithmic ranking taking into account verification score, tutor rating, completed sessions, and mutual weekday availability.
5. **Session Lifecycle Management:** Mutual confirmation workflow, cancellation with automatic escrow refund, and administrator dispute arbitration.
6. **Direct Skill Swap:** Complementary barter discovery and 0-credit schedule coordination.
7. **Learning Materials Repository:** Type and size-validated file uploads (PDF, PPTX, Doc, Image, Code) with access control.
8. **Multi-Criteria Tutor Rating:** Evaluating subject knowledge, clarity, communication, and time management.
9. **Gamification & Badges:** Backend-validated achievement triggers for teaching hours, ratings, and milestones.
10. **SkillSwap AI Assistant:** Database-aware interactive chatbot guiding students on platform policies and credits.
11. **Administrator Console:** Platform KPIs, verification review actions, dispute arbitrations, student account suspensions, and ledger audit table.

---

## 7. Non-Functional Requirements
- **Security:** Argon2/PBKDF2 password hashing, JWT stateless tokens, CSRF protection, CORS whitelisting, and SQL injection prevention via Django ORM.
- **Concurrency & Ledger Integrity:** Database row-level locks (`select_for_update`) within `transaction.atomic()` preventing race conditions during simultaneous escrow and payout operations.
- **Performance:** Sub-200ms REST API response times; lightweight client bundle (Vite React + Tailwind CSS).
- **Responsive Design:** SaaS-grade responsive layout optimized for desktop, tablet, and mobile browsers with seamless light and dark mode toggling.

---

## 8. Software and Hardware Requirements
- **Frontend Stack:** React 18, Vite 6, Tailwind CSS 3, React Router DOM 7, Framer Motion, Lucide React, Recharts, Axios.
- **Backend Stack:** Python 3.13, Django 5.2, Django REST Framework 3.18, Simple JWT 5.5, django-cors-headers, python-dotenv, dj-database-url, psycopg2-binary.
- **Database:** PostgreSQL (with automatic zero-config SQLite development fallback).
- **Hardware Minimums:** 2.0 GHz Dual-Core Processor, 4 GB RAM, 2 GB free disk space.

---

## 9. Credit Calculation & Escrow Rules
1. **Registration Grant:** Every student receives exactly 50 credits recorded in the immutable ledger.
2. **Hourly Teaching Rates:**
   - **Beginner Level:** 10 Credits / Hour
   - **Intermediate Level:** 15 Credits / Hour
   - **Advanced Level:** 20 Credits / Hour
3. **Escrow Hold Mechanism:** When a learner requests a session, the required credit amount is locked (`available_balance -= amount`, `escrow_balance += amount`). The learner cannot spend these credits elsewhere.
4. **Settlement on Mutual Confirmation:** Only when both learner and tutor confirm session completion does the backend atomically transfer credits from the learner's escrow into the tutor's available balance.
5. **Refund Guarantee:** If a session is cancelled by either party or declined by the tutor, escrow credits are refunded immediately to the learner's available balance.
6. **Direct Skill Swap:** When two students exchange skills directly, credits charged = **0**.

---

## 10. Module Descriptions
1. **`accounts`:** Custom User model, StudentProfile, multi-step registration, JWT authentication, and weekly availability slots.
2. **`skills`:** SkillCategory taxonomy, Skill definitions, StudentTeachingSkill records, learning interests, and intelligent matching algorithms.
3. **`verification`:** Question bank, dynamic assessment evaluation, and verification records (Verified, Provisional, Rejected).
4. **`wallet`:** CreditWallet entity, immutable CreditTransaction ledger, and atomic WalletService methods.
5. **`learning_sessions`:** LearningSession state machine, mutual completion verification, cancellation handlers, and Dispute records.
6. **`swaps`:** SkillSwapRequest model, mutual match identification logic, and zero-credit barter workflows.
7. **`materials`:** Secure file and link uploads with type validation (PDF, PPTX, Docs, Images, Code) and download links.
8. **`reviews`:** 4-criteria reviews (Knowledge, Clarity, Communication, Time Management) with live tutor rating updates.
9. **`notifications`:** In-app notification center tracking session requests, credit transfers, and verification results.
10. **`achievements`:** Gamification engine awarding verifiable campus badges based on actual database activity.
11. **`chatbot`:** SkillSwap Assistant combining database context with external LLM API integration and intelligent fallback logic.
12. **`administration`:** Role-protected admin portal for KPI monitoring, student management, dispute resolution, and ledger audit.

---

## 11. Future Scope
- **WebRTC Integrated Live Classrooms:** In-browser encrypted audio/video calling and shared collaborative code editors.
- **Inter-College Credit Roaming:** Allowing verified students to exchange credits across partner universities.
- **Automated AI Practical Code Sandbox:** Real-time containerized code compilation for programming assessments.
