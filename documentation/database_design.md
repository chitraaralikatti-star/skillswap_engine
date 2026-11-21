# SkillSwap Database Design & Normalized Relational Schema
**Project Theme:** Theme 3 – Ledger and Money Systems  
**Engine:** MongoDB via `django_mongodb_backend` (Local MongoDB `skillswap`, configured via `DATABASE_URL`)

---

## 1. Relational Schema Specification

### 1. `accounts_user`
- `id`: BigAutoField (Primary Key)
- `username`: VarChar(150), Unique, Indexed
- `email`: VarChar(254), Unique, Indexed
- `password`: VarChar(128) (Hashed with PBKDF2/SHA256)
- `first_name`: VarChar(150)
- `last_name`: VarChar(150)
- `role`: VarChar(20) [Choices: `student`, `admin`]
- `phone_number`: VarChar(20), Nullable
- `is_verified_student`: Boolean, Default `False`
- `is_active`: Boolean, Default `True`
- `is_staff`: Boolean, Default `False`
- `is_superuser`: Boolean, Default `False`
- `created_at`: DateTime, Auto_now_add
- `updated_at`: DateTime, Auto_now

### 2. `accounts_studentprofile`
- `id`: BigAutoField (Primary Key)
- `user_id`: OneToOneField -> `accounts_user(id)`, On_Delete Cascade
- `college_name`: VarChar(200)
- `department`: VarChar(150)
- `year_of_study`: VarChar(30) [Choices: `1st Year`, `2nd Year`, `3rd Year`, `4th Year`, `Postgraduate`, `Alumni`]
- `bio`: TextField
- `profile_picture`: VarChar(100) (File path in `media/profiles/`), Nullable
- `learning_goals`: TextField
- `preferred_categories`: JSONField
- `teaching_mode`: VarChar(20) [Choices: `online`, `offline`, `hybrid`]
- `total_teaching_hours`: Decimal(6, 1), Default `0.0`
- `total_learning_hours`: Decimal(6, 1), Default `0.0`
- `average_rating`: Decimal(3, 2), Default `5.00`
- `total_reviews`: Integer, Default `0`
- `completed_sessions`: Integer, Default `0`
- `created_at`: DateTime
- `updated_at`: DateTime

### 3. `accounts_availability`
- `id`: BigAutoField (Primary Key)
- `user_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `day_of_week`: Integer [0=Monday ... 6=Sunday]
- `start_time`: Time
- `end_time`: Time
- `is_active`: Boolean, Default `True`
- **Index:** `(user_id, day_of_week)`

### 4. `skills_skillcategory`
- `id`: BigAutoField (Primary Key)
- `name`: VarChar(100), Unique
- `slug`: SlugField(120), Unique, Indexed
- `description`: TextField
- `icon`: VarChar(50) (Lucide Icon name)
- `display_order`: Integer, Default `0`
- `created_at`: DateTime

### 5. `skills_skill`
- `id`: BigAutoField (Primary Key)
- `category_id`: ForeignKey -> `skills_skillcategory(id)`, On_Delete Cascade
- `name`: VarChar(150), Unique, Indexed
- `description`: TextField
- `default_topics`: JSONField (List of standard syllabus topics)
- `is_active`: Boolean, Default `True`
- `created_at`: DateTime

### 6. `skills_studentteachingskill`
- `id`: BigAutoField (Primary Key)
- `student_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `experience_level`: VarChar(20) [Choices: `beginner`, `intermediate`, `advanced`]
- `experience_duration`: VarChar(100)
- `description`: TextField
- `topics_offered`: JSONField
- `teaching_language`: VarChar(50), Default `'English'`
- `preferred_duration_minutes`: Integer, Default `60`
- `certificate_file`: VarChar(100), Nullable
- `portfolio_link`: URLField, Nullable
- `hourly_rate`: Integer (10 for beginner, 15 for intermediate, 20 for advanced)
- `verification_status`: VarChar(20) [Choices: `pending`, `verified`, `provisional`, `rejected`]
- `verification_score`: Integer, Default `0`
- `verification_note`: TextField
- `is_published`: Boolean, Default `False`
- `total_sessions_taught`: Integer, Default `0`
- `average_rating`: Decimal(3, 2), Default `5.00`
- `created_at`: DateTime
- `updated_at`: DateTime
- **Constraint:** Unique(`student_id`, `skill_id`)

### 7. `skills_studentlearninginterest`
- `id`: BigAutoField (Primary Key)
- `student_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `preferred_level`: VarChar(20)
- `preferred_duration_minutes`: Integer, Default `60`
- `learning_goals`: TextField
- `created_at`: DateTime
- **Constraint:** Unique(`student_id`, `skill_id`)

### 8. `verification_assessmentquestion`
- `id`: BigAutoField (Primary Key)
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `question_type`: VarChar(20) [Choices: `mcq`, `descriptive`, `code_task`]
- `question_text`: TextField
- `options`: JSONField (Array of `{"id": "A", "text": "..."}`)
- `correct_option_id`: VarChar(10), Nullable
- `explanation`: TextField
- `sample_answer`: TextField
- `difficulty`: VarChar(20) [Choices: `easy`, `medium`, `hard`]
- `created_at`: DateTime

### 9. `verification_skillassessment`
- `id`: BigAutoField (Primary Key)
- `student_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `teaching_skill_id`: ForeignKey -> `skills_studentteachingskill(id)`, On_Delete Cascade
- `score`: Integer, Default `0`
- `status`: VarChar(20) [Choices: `in_progress`, `submitted`, `evaluated`]
- `started_at`: DateTime
- `completed_at`: DateTime, Nullable
- `automated_feedback`: TextField

### 10. `verification_assessmentanswer`
- `id`: BigAutoField (Primary Key)
- `assessment_id`: ForeignKey -> `verification_skillassessment(id)`, On_Delete Cascade
- `question_id`: ForeignKey -> `verification_assessmentquestion(id)`
- `selected_option_id`: VarChar(10), Nullable
- `descriptive_response`: TextField, Nullable
- `is_correct`: Boolean, Default `False`
- `points_awarded`: Float, Default `0.0`
- `evaluator_notes`: TextField

### 11. `verification_skillverification`
- `id`: BigAutoField (Primary Key)
- `teaching_skill_id`: OneToOneField -> `skills_studentteachingskill(id)`, On_Delete Cascade
- `assessment_id`: ForeignKey -> `verification_skillassessment(id)`, Nullable
- `outcome`: VarChar(30) [Choices: `verified`, `provisional`, `admin_approved`, `rejected`]
- `score`: Integer, Default `0`
- `reviewed_by_id`: ForeignKey -> `accounts_user(id)`, Nullable
- `review_comments`: TextField
- `verified_at`: DateTime
- `updated_at`: DateTime

### 12. `wallet_creditwallet`
- `id`: BigAutoField (Primary Key)
- `user_id`: OneToOneField -> `accounts_user(id)`, On_Delete Cascade
- `available_balance`: Integer, Default `50`
- `escrow_balance`: Integer, Default `0`
- `total_earned`: Integer, Default `0`
- `total_spent`: Integer, Default `0`
- `created_at`: DateTime
- `updated_at`: DateTime
- **Constraint:** Check(`available_balance >= 0`), Check(`escrow_balance >= 0`)

### 13. `wallet_credittransaction`
- `id`: BigAutoField (Primary Key)
- `transaction_id`: UUID, Unique, Indexed
- `sender_id`: ForeignKey -> `accounts_user(id)`, Nullable
- `receiver_id`: ForeignKey -> `accounts_user(id)`, Nullable
- `session_id`: Integer, Nullable, Indexed
- `transaction_type`: VarChar(30) [Choices: `registration_bonus`, `session_escrow`, `session_payout`, `session_refund`, `admin_adjustment`, `dispute_resolution`]
- `amount`: Integer
- `sender_previous_balance`: Integer, Nullable
- `sender_updated_balance`: Integer, Nullable
- `receiver_previous_balance`: Integer, Nullable
- `receiver_updated_balance`: Integer, Nullable
- `status`: VarChar(20) [Choices: `completed`, `pending`, `failed`, `reversed`]
- `description`: TextField
- `timestamp`: DateTime, Auto_now_add, Indexed
- **Indexes:** `(transaction_type, status)`, `(sender_id, receiver_id)`, `timestamp`

### 14. `learning_sessions_learningsession`
- `id`: BigAutoField (Primary Key)
- `teacher_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `learner_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `teaching_skill_id`: ForeignKey -> `skills_studentteachingskill(id)`, Nullable
- `topics`: JSONField
- `scheduled_date`: Date
- `start_time`: Time
- `duration_minutes`: Integer, Default `60`
- `credit_amount`: Integer
- `session_mode`: VarChar(20) [Choices: `online`, `offline`]
- `meeting_link_or_location`: VarChar(300)
- `notes`: TextField
- `status`: VarChar(20) [Choices: `requested`, `accepted`, `in_progress`, `completed`, `rejected`, `cancelled`, `disputed`]
- `teacher_completed_confirmed`: Boolean, Default `False`
- `learner_completed_confirmed`: Boolean, Default `False`
- `completion_timestamp`: DateTime, Nullable
- `cancelled_by_id`: ForeignKey -> `accounts_user(id)`, Nullable
- `cancellation_reason`: TextField
- `created_at`: DateTime
- `updated_at`: DateTime
- **Indexes:** `status`, `(scheduled_date, start_time)`

### 15. `learning_sessions_dispute`
- `id`: BigAutoField (Primary Key)
- `session_id`: OneToOneField -> `learning_sessions_learningsession(id)`, On_Delete Cascade
- `raised_by_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `reason`: TextField
- `evidence_text`: TextField
- `status`: VarChar(30) [Choices: `open`, `resolved_refund`, `resolved_payout`, `resolved_split`, `dismissed`]
- `admin_notes`: TextField
- `resolved_by_id`: ForeignKey -> `accounts_user(id)`, Nullable
- `created_at`: DateTime
- `resolved_at`: DateTime, Nullable

### 16. `swaps_skillswaprequest`
- `id`: BigAutoField (Primary Key)
- `initiator_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `receiver_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `initiator_skill_offered_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `receiver_skill_requested_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `initiator_topics`: JSONField
- `receiver_topics`: JSONField
- `scheduled_date`: Date, Nullable
- `start_time`: Time, Nullable
- `session_duration_minutes`: Integer, Default `60`
- `meeting_link`: VarChar(300)
- `status`: VarChar(20) [Choices: `pending`, `accepted`, `in_progress`, `completed`, `rejected`, `cancelled`]
- `message`: TextField
- `initiator_confirmed_completed`: Boolean, Default `False`
- `receiver_confirmed_completed`: Boolean, Default `False`
- `completed_at`: DateTime, Nullable
- `created_at`: DateTime
- `updated_at`: DateTime

### 17. `materials_learningmaterial`
- `id`: BigAutoField (Primary Key)
- `uploaded_by_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `session_id`: ForeignKey -> `learning_sessions_learningsession(id)`, Nullable
- `title`: VarChar(200)
- `description`: TextField
- `material_type`: VarChar(20) [Choices: `pdf`, `presentation`, `document`, `image`, `link`, `code`]
- `file`: VarChar(100), Nullable
- `external_link`: URLField, Nullable
- `file_size_bytes`: BigInteger, Default `0`
- `is_public`: Boolean, Default `False`
- `created_at`: DateTime

### 18. `reviews_review`
- `id`: BigAutoField (Primary Key)
- `session_id`: OneToOneField -> `learning_sessions_learningsession(id)`, On_Delete Cascade
- `reviewer_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `teacher_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `skill_id`: ForeignKey -> `skills_skill(id)`, On_Delete Cascade
- `subject_knowledge`: Integer (1 to 5)
- `teaching_clarity`: Integer (1 to 5)
- `communication`: Integer (1 to 5)
- `time_management`: Integer (1 to 5)
- `overall_rating`: Decimal(3, 2)
- `feedback_comment`: TextField
- `created_at`: DateTime

### 19. `notifications_notification`
- `id`: BigAutoField (Primary Key)
- `user_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `title`: VarChar(200)
- `message`: TextField
- `notification_type`: VarChar(30)
- `link_url`: VarChar(200)
- `is_read`: Boolean, Default `False`
- `created_at`: DateTime

### 20. `achievements_achievement`
- `id`: BigAutoField (Primary Key)
- `name`: VarChar(120), Unique
- `slug`: SlugField(140), Unique
- `description`: TextField
- `icon`: VarChar(50)
- `badge_color`: VarChar(30)
- `condition_type`: VarChar(40)
- `threshold`: Integer, Default `1`
- `created_at`: DateTime

### 21. `achievements_studentachievement`
- `id`: BigAutoField (Primary Key)
- `student_id`: ForeignKey -> `accounts_user(id)`, On_Delete Cascade
- `achievement_id`: ForeignKey -> `achievements_achievement(id)`, On_Delete Cascade
- `earned_at`: DateTime
- **Constraint:** Unique(`student_id`, `achievement_id`)

### 22. `chatbot_chatconversation` & `chatbot_chatmessage`
- `id`: BigAutoField (Primary Key)
- `user_id`: ForeignKey -> `accounts_user(id)`
- `role`: VarChar(20) [Choices: `user`, `assistant`, `system`]
- `content`: TextField
- `timestamp`: DateTime
