# Database Documentation

## Overview
FitSteps utilizes a robust, relational data storage architecture. The primary database is **PostgreSQL**, hosted externally via Neon serverless Postgres.

## Schema Architecture

The database is composed of multiple interconnected tables, designed using SQLAlchemy ORM models:

### 1. `users` Table
The core table holding all account information.
- **Columns:** `id`, `email`, `hashed_password`, `name`, `created_at`
- **Gamification Tracking:** Stores running tallies for `current_streak`, `longest_streak`, and `last_active_date` to prevent recalculating the entire activity log on every request.

### 2. `activities` Table
The transaction ledger of all fitness entries.
- **Relationships:** Links to the `users` table via `user_id`. (A User has a one-to-many relationship with Activities).
- **Columns:** `id`, `category` (e.g., walking, running), `steps`, `duration`, `notes`, `activity_date`

### 3. `goals` Table
Tracks user-defined daily targets.
- **Columns:** `id`, `user_id`, `target_steps`, `status` (active, achieved, failed), `start_date`, `end_date`

### 4. Gamification Tables (`badges` & `user_badges`)
- **`badges`:** A static catalog of available milestones (e.g., "10k Club"). Contains `name`, `description`, and `icon_url`.
- **`user_badges`:** A bridge (join) table connecting `users` to `badges`. This table ensures a user can only earn a specific badge once. It tracks exactly *when* the badge was earned via an `earned_at` timestamp.

### 5. `ai_recommendations` Table
Logs coaching suggestions provided by the Gemini AI.
- **Columns:** `id`, `user_id`, `recommended_steps`, `reason`, `created_at`

## Migrations (Alembic)
Changes to the database structure (like adding a new table or altering a column) are never executed directly via SQL commands. Instead, Alembic is used. Alembic reads changes in the SQLAlchemy `models.py` files, auto-generates Python migration scripts, and safely applies those structural changes sequentially to the production PostgreSQL database.
