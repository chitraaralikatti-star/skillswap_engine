# SkillSwap – Installation & Execution Guide
**Project:** SkillSwap – Student Skill Exchange and Credit Ledger System  
**Theme:** Theme 3 – Ledger and Money Systems  
**Operating System:** Windows (PowerShell & CMD compatible)

---

## 1. Quick Start Demonstration Credentials

For instant demonstration and technical evaluation, the system comes pre-seeded with realistic student accounts and an administrator portal:

| Role | Username | Password | Purpose & Specialties |
|---|---|---|---|
| **Student (Tutor)** | `arun` | `Password@123` | Python Tutor (Verified 92%). Has completed sessions & reviews. |
| **Student (Tutor)** | `priya` | `Password@123` | Photoshop Tutor (Verified 96%). Mutual barter match with Arun! |
| **Student (Tutor)** | `rahul` | `Password@123` | React.js Tutor (Verified 88%). Mutual barter match with Ananya! |
| **Student (Tutor)** | `ananya` | `Password@123` | UI/UX in Figma Tutor (Verified 90%). |
| **Administrator** | `admin` | `Admin@123` | Full access to `/admin-portal`, disputes, & audit ledger. |

---

## 2. Backend Execution Guide (Django REST Framework with MongoDB)

### Prerequisites:
- Python 3.10+ (Python 3.13 tested)
- MongoDB Server 7.0+ / 8.0+ running locally on port 27017

### Step 1: Open PowerShell / Command Prompt
```powershell
cd d:\skillswap\backend
```

### Step 2: Activate Virtual Environment
```powershell
# In PowerShell:
.\venv\Scripts\Activate.ps1

# Or in CMD:
venv\Scripts\activate.bat
```

### Step 3: Configure Environment Variables
A configured `.env` is already configured for MongoDB:
```ini
# d:\skillswap\backend\.env
DATABASE_URL=mongodb://localhost:27017/skillswap
DJANGO_SECRET_KEY=your-django-secret-key-placeholder
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1,testserver,*
FRONTEND_URL=http://localhost:5173
LLM_API_KEY=YOUR_GOOGLE_CLOUD_API_KEY
```

### Step 4: Start MongoDB (If Not Already Running as Service)
```powershell
& "C:\Program Files\MongoDB\Server\8.2\bin\mongod.exe" --dbpath "d:\skillswap\backend\mongo_data" --port 27017
```

### Step 5: Run Migrations and Seed Demo Data
```powershell
python manage.py makemigrations
python manage.py migrate
python manage.py seed_data
```

### Step 5: Run Automated Tests
```powershell
python manage.py test accounts
```

### Step 6: Start the Django Backend Server
```powershell
python manage.py runserver 127.0.0.1:8000
```
Backend API will be live at: **`http://127.0.0.1:8000/api/`**  
Django Admin Console: **`http://127.0.0.1:8000/django-admin/`**

---

## 3. Frontend Execution Guide (React.js + Vite)

### Prerequisites:
- Node.js 18+ (Node v24 tested)
- npm 9+

### Step 1: Open a Second Terminal Window
```powershell
cd d:\skillswap\frontend
```

### Step 2: Start Vite Development Server
```powershell
npm run dev
```
The application will launch immediately at: **`http://localhost:5173/`**

---

## 4. Key Workflows to Demonstrate During Evaluation

1. **New Student Registration (5-Step Wizard):**
   - Navigate to `http://localhost:5173/register`
   - Complete Step 1 to Step 5.
   - Notice the initial **50 Welcome Credits** awarded and logged in the immutable ledger.
2. **Dynamic Skill Verification Assessment:**
   - Log in as any student or register a new teaching skill in **Teach Skills** (`/teach`).
   - Click **Take Skill Assessment**.
   - Solve tailored MCQs and descriptive tasks.
   - Score >=80% automatically grants the **Verified Tutor** badge and publishes the listing.
3. **Intelligent Skill Matching & Session Escrow:**
   - Go to **Learn Skills** (`/learn`).
   - Notice top matching tutors ranked with clear match explanations.
   - Click **Request Session**. The required credits (e.g. 15 Cr) are held in **Escrow**.
4. **Mutual Completion & Payout Release:**
   - Go to **My Sessions** (`/sessions`).
   - Notice completion status. Once both learner and tutor mark completed, credits atomically move from escrow into the tutor's wallet.
5. **Direct 0-Credit Skill Swap:**
   - Go to **Skill Swap** (`/swaps`).
   - Notice discovered complementary barter pairs (e.g. Arun & Priya).
   - Propose a swap. Notice **0 credits** are charged.
6. **Immutable Transaction Ledger Audit:**
   - Go to **Credit Wallet** (`/wallet`).
   - Review UUID-audited double-entry transactions (Earned, Spent, Escrow, Refunds, Bonuses).
7. **SkillSwap AI Assistant:**
   - Click the floating **SkillSwap Assistant** bot at the bottom-right of any page.
   - Ask: *"What is my available balance?"* or *"How do credits work in escrow?"*
8. **Campus Administration Portal:**
   - Log in as `admin` / `Admin@123` and visit `/admin-portal`.
   - Review pending verifications, arbitrate disputes, toggle student account statuses, and audit platform KPIs.
