# Authentication Documentation

## Overview
FitSteps employs a stateless, token-based authentication system using **JSON Web Tokens (JWT)**. This ensures that the backend remains infinitely scalable without needing to manage session memory.

## 1. Backend Authentication (FastAPI)

### Password Security
- Passwords are **never** stored in plain text. 
- During registration (`POST /auth/register`), the `passlib` library hashes the password using the `bcrypt` algorithm.
- During login (`POST /auth/login`), the backend compares the submitted plain-text password against the stored bcrypt hash using `pwd_context.verify()`.

### JWT Generation
- Upon a successful login, the backend generates a JWT.
- The token payload (the "claims") contains the user's database `id` (as the `sub` claim) and an expiration timestamp (`exp`).
- The token is cryptographically signed using the `SECRET_KEY` from the environment variables and the `HS256` algorithm.

### Protecting Routes
- Sensitive API endpoints (e.g., fetching a user's activities) are protected using FastAPI's dependency injection: `Depends(get_current_user)`.
- When a request hits a protected route, `get_current_user` intercepts it, extracts the `Bearer` token from the `Authorization` header, decodes it, verifies the cryptographic signature, and queries the database for the user. If any step fails, it throws a `401 Unauthorized`.

## 2. Frontend Authentication (React)

### Global State Management
- Authentication state is managed globally using React's Context API (`src/context/AuthContext.jsx`).
- The `AuthContext` provides `user`, `login()`, and `logout()` functions to any component in the application.

### Token Persistence & Axios Interceptor
- When a user logs in, the JWT is stored in the browser's `localStorage`. This ensures the user stays logged in even if they refresh the page or close the tab.
- An **Axios Interceptor** (`src/services/api.js`) automatically attaches this token to every outgoing HTTP request header:
  ```javascript
  config.headers.Authorization = `Bearer ${token}`;
  ```
- If the token expires and the server returns a `401`, the interceptor catches it, clears `localStorage`, and forces a logout, redirecting the user to the login screen safely.
