# Deployment Documentation

## Overview
FitSteps utilizes a modern, decoupled deployment architecture. The frontend, backend, and database are hosted on three separate platforms specialized for their respective workloads, completely independent of one another.

## 1. Frontend: Vercel
The React SPA (Single Page Application) is hosted on **Vercel**.
- **Build Process:** Vercel automatically hooks into the GitHub repository. Whenever code is pushed to the `main` branch, Vercel pulls it, runs `npm install`, and executes the Vite build step (`npm run build`).
- **Delivery:** The resulting static HTML/CSS/JS files are instantly distributed across Vercel's global CDN (Content Delivery Network), ensuring users around the world load the UI incredibly fast.

## 2. Backend: Render
The FastAPI server is hosted as a Web Service on **Render.com**.
- **Execution:** Render acts as a persistent virtual server. It runs the Python environment (`uvicorn main:app --host 0.0.0.0 --port $PORT`), constantly listening for incoming API requests from the Vercel frontend.
- **Environment Management:** All sensitive keys (`DATABASE_URL`, `SECRET_KEY`, `GEMINI_API_KEY`) are stored safely in Render's Environment Variables dashboard, preventing them from being exposed in GitHub.
- **Continuous Deployment:** Similar to Vercel, Render automatically triggers a manual or automatic rebuild whenever the backend GitHub repository receives new commits.

## 3. Database: Neon
The PostgreSQL database is hosted on **Neon**, a serverless Postgres provider.
- **Connection:** The Render backend connects to the Neon database securely using the standard `postgresql://` connection string over the internet.
- **Serverless Architecture:** Neon scales computing resources automatically based on database load, making it highly efficient for sporadic API requests.
