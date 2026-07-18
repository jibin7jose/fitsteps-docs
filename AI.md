# AI Integration Documentation

## Overview
FitSteps integrates Google's **Gemini AI** (`gemini-flash-latest`) to act as a personalized fitness coach. This integration is completely server-side, ensuring API keys are securely hidden from the client.

## Logic Flow
1. **Trigger:** When the frontend Dashboard loads, it calls `POST /ai/recommendation`.
2. **Context Gathering:** The backend fetches all activities logged by the user over the last 7 days from the PostgreSQL database.
3. **Data Formatting:** The activities are parsed into a lightweight JSON array (dates, categories, steps, durations).
4. **Prompt Engineering:** The backend constructs a highly specific prompt instructing the Gemini model to:
   - Analyze the provided 7-day activity history.
   - Suggest a realistic step goal for tomorrow.
   - Provide an encouraging reason based on their trend.
   - Suggest two context-aware activity breaks.
   - Output the response *strictly* as a JSON object.
5. **Execution:** The prompt is sent to Google's API using the `google-generativeai` SDK.
6. **Data Storage:** The generated recommendation is saved to the `ai_recommendations` database table so the user's coaching history is preserved.

## Safety & Fallback Mechanism
To ensure the application never crashes if the AI service goes down or if the `GEMINI_API_KEY` is missing from the environment:
- **Missing Key Check:** At backend startup, the system checks for `settings.GEMINI_API_KEY`. If it's missing, the `/recommendation` endpoint is dynamically mounted with a hardcoded fallback function.
- **Try/Catch Block:** If the API key exists but the Gemini API throws a 500 error or a timeout, a try/catch block intercepts the exception, logs it, and returns a graceful fallback JSON response to the frontend.
- **Frontend Handling:** The frontend displays `"Calculating..."` while waiting for the promise to resolve, preventing UI layout shifts.
