# Frontend Documentation

## Overview
FitSteps is a Single Page Application (SPA) built with React. It provides a highly responsive, modern, and interactive user interface without requiring full page reloads, connecting seamlessly to the remote backend API.

## Key Technologies
- **React 18 + Vite:** The foundation of the app. Vite was chosen over Create React App (CRA) for its drastically faster Hot Module Replacement (HMR) and optimized production builds.
- **Tailwind CSS:** Used for all styling. It allows for rapid, utility-first UI development directly within JSX files, utilizing custom configurations for the slate and emerald color palettes.
- **React Router DOM v6:** Handles client-side navigation between pages (Dashboard, Leaderboard, etc.) securely wrapping protected routes.
- **Recharts:** A composable charting library built on React components used to render the dynamic activity trend lines on the Dashboard.
- **Axios:** A promise-based HTTP client used to fetch data from the FastAPI backend, utilizing global interceptors for JWT injection.

## Component Architecture
- **Global Context:** `AuthContext.jsx` manages the global authentication state across the entire component tree.
- **Protected Routes:** `AuthRoute.jsx` wraps sensitive pages. If a user tries to load the Dashboard without a valid JWT, this component intercepts the render and redirects them to the Login page.
- **Pages (Views):** The `src/pages` directory contains the main views (e.g., `Dashboard.jsx`, `Goals.jsx`). These are complex components that handle their own data fetching (`useEffect`) and local state management (`useState`).
- **Data Visualization:** The Dashboard fetches raw activities, mathematically groups them by calendar date, and maps them into the precise object array structure demanded by Recharts to render the visual activity graph.
