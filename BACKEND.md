# Backend Documentation

## Overview
The FitSteps backend is a high-performance RESTful API built with **FastAPI** (Python 3.9+). It serves as the authoritative source of truth for all business logic, data persistence, gamification rules, and AI integration.

## Key Technologies
- **FastAPI:** Chosen for its asynchronous routing, extreme performance, and automatic OpenAPI documentation generation.
- **SQLAlchemy:** A powerful Object Relational Mapper (ORM) used to interact with the PostgreSQL database using Python objects.
- **Alembic:** A database migration tool that versions database schema changes (like adding a new column) so they can be applied predictably across environments.
- **Pydantic:** Strictly validates incoming JSON requests and formats outgoing JSON responses, throwing automatic `422 Unprocessable Entity` errors if frontend payloads are malformed.

## Core Modules

### 1. `api/activities.py`
Handles logging new fitness activities. When a user submits an activity (e.g., 5,000 steps), this module saves it to the database and immediately invokes gamification logic to evaluate if any streaks were extended or badges were earned.

### 2. `api/analytics.py`
Instead of pulling thousands of raw activity rows into Python memory to calculate averages, this module leverages SQLAlchemy to offload the heavy mathematical lifting directly to the PostgreSQL engine. It uses complex SQL grouping (`func.date`, `date_trunc`) to calculate the user's all-time Best Day, Best Week, and Weekly Average in milliseconds.

### 3. `api/gamification.py`
The absolute authority on user achievements. It tracks two key metrics:
- **Streaks:** Calculates consecutive days of activity. If a user misses a day, their streak is reset.
- **Badges:** Uses a bridge table (`user_badges`) to award badges. It queries this table to ensure a user is never awarded the same milestone badge twice.

### 4. `api/ai.py`
Connects to the Google Gemini API to generate personalized daily step goals and fitness coaching based on the user's trailing 7-day activity history.
