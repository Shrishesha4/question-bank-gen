# Project Development Documentation: Question Bank Generator

**Project:** AI-Powered Question Bank Generator  
**Team Size:** 2 Developers  
**Timeline:** 8 Weeks
**Date:** January 2026

---

## 1. Executive Summary

This document outlines the chronological development process of the Question Bank Generator, a full-stack application designed to automate the creation of educational assessments using Generative AI (Google Gemini). The project was executed by a two-person team, dividing responsibilities between Backend/AI and Frontend/UI, with shared responsibility for Architecture and DevOps.

## 2. Development Phases

### Phase 1: Inception & Architecture (Week 1)

**Goal:** Define project scope, select technology stack, and design system architecture.

*   **Requirements Gathering:**
    *   Identified the need for a tool that can generate questions from both raw topics and uploaded PDF documents.
    *   Defined core question types: Multiple Choice (MCQ), True/False, Short Answer.
    *   Established the need for a "Human-in-the-loop" feel via real-time streaming and validation.

*   **Tech Stack Selection:**
    *   **Backend:** Python/FastAPI was chosen for its speed, async capabilities (crucial for AI streaming), and excellent ecosystem for AI/ML libraries.
    *   **Frontend:** Svelte 5 (with Runes) was selected for its reactivity and performance, paired with Tailwind CSS for rapid styling.
    *   **AI Engine:** Google Gemini Pro was selected for its large context window (useful for PDF analysis) and cost-effectiveness.
    *   **Database:** SQLite for development simplicity, with SQLAlchemy ORM to allow easy migration to PostgreSQL in production.

*   **Database Schema Design:**
    *   Designed `User` table for RBAC (Admin/User).
    *   Designed `QuestionSet` and `Question` tables to store generated history.
    *   Designed `GenerationSession` to track usage and analytics.

### Phase 2: Backend Foundation (Weeks 2-3)

**Goal:** Build a robust API to handle user management and data persistence.

*   **Core Setup:**
    *   Initialized FastAPI project structure with a service-layer pattern (`core`, `services`, `routers`).
    *   Configured SQLAlchemy `engine` and `SessionLocal` in `backend/core/database.py`.

*   **Authentication System:**
    *   Implemented `backend/services/auth.py` using `python-jose` for JWT token generation.
    *   Created login/register endpoints and dependency injection for `get_current_user`.
    *   Implemented password hashing using `passlib`.

*   **PDF Processing:**
    *   Developed `backend/core/pdf_processor.py` using `pypdf` to extract clean text from uploaded documents.
    *   Implemented basic text cleaning to remove artifacts and headers/footers to save token usage.

### Phase 3: The AI Core & Logic (Week 4)

**Goal:** Implement the intelligence layer for generating and validating questions.

*   **Gemini Integration:**
    *   Set up `backend/core/llm.py` to handle Google Gemini API interactions.
    *   Implemented retry logic and error handling for API rate limits.

*   **Prompt Engineering:**
    *   Designed complex system prompts in `backend/services/generator.py` to enforce JSON output formats.
    *   Created specific prompts for different difficulty levels (Easy, Medium, Hard) and Bloom's Taxonomy alignment.

*   **Validation Layer:**
    *   Built `QuestionValidator` service. This is a secondary AI pass that checks generated questions for:
        *   Factual correctness.
        *   Relevance to the source text.
        *   Formatting consistency.
    *   *Innovation:* Implemented a feedback loop where invalid questions are automatically regenerated or flagged.

*   **Local ML Optimization:**
    *   Implemented `backend/core/local_ml.py` for caching and deduplication to reduce API costs and latency.

### Phase 4: Frontend Skeleton & UI Library (Week 5)

**Goal:** Create a modern, responsive user interface.

*   **Svelte 5 Setup:**
    *   Initialized the project with Vite.
    *   Configured `jsconfig.json` for path aliases.

*   **Component Library:**
    *   Adopted a "headless" approach using **Bits UI** for accessible primitives.
    *   Styled components using **Tailwind CSS 4**.
    *   Built reusable atomic components in `src/lib/components/ui`:
        *   `Button`, `Input`, `Label`, `Select`, `Textarea`.
        *   `Card` components for consistent layout of question sets.

*   **State Management:**
    *   Implemented Svelte Stores (`src/stores/`) for global state:
        *   `auth.js`: Manages JWT tokens and user profile.
        *   `theme.js`: Handles Dark/Light mode preferences.

### Phase 5: Real-Time Generation & Streaming (Week 6)

**Goal:** Connect Frontend and Backend with a seamless streaming experience.

*   **Streaming Implementation:**
    *   The team faced a challenge: Waiting 30+ seconds for a full question set was bad UX.
    *   **Solution:** Implemented StreamingResponse in FastAPI.
    *   Frontend was updated to parse incoming JSON chunks and render questions one by one as they are generated.

*   **Dashboard Development:**
    *   **User Dashboard:** View history, start new generation.
    *   **Admin Dashboard:** Added charts and tables to monitor system usage and manage users.

### Phase 6: Refinement & Polish (Week 7)

**Goal:** Ensure stability, responsiveness, and code quality.

*   **Mobile Responsiveness:**
    *   Refined Tailwind classes to ensure the complex forms and result cards look good on mobile devices.
    *   Implemented a responsive Navbar (`src/lib/Navbar.svelte`).

*   **Error Handling:**
    *   Added comprehensive error toasts (using `svelte-sonner` or similar custom implementation) for API failures.
    *   Handled edge cases like "PDF too large" or "Topic too vague".

### Phase 7: DevOps & Deployment (Week 8)

**Goal:** Containerize the application for easy distribution.

*   **Dockerization:**
    *   Created `Dockerfile` for a multi-stage build (building frontend assets, then serving with Nginx/Python).
    *   Wrote `docker-compose.yml` to orchestrate the Backend and Frontend services.
    *   Created `start.sh` for a one-click startup experience.

*   **Nginx Configuration:**
    *   Configured `nginx.conf` to serve static files and reverse-proxy `/api` requests to the FastAPI backend.
    *   Added SSL configuration templates (`nginx-ssl.conf`) for production readiness.

---

## 3. References & Resources Used

During the development of this project, the following resources were consulted:

1.  **FastAPI Documentation**: [https://fastapi.tiangolo.com/](https://fastapi.tiangolo.com/)
    *   *Usage:* Async dependencies, OAuth2 implementation, and StreamingResponse.
2.  **Svelte 5 Preview Docs**: [https://svelte.dev/docs/svelte/overview](https://svelte.dev/docs/svelte/overview)
    *   *Usage:* Understanding the new Runes API (`$state`, `$derived`) for reactive state management.
3.  **Google AI Studio (Gemini API)**: [https://ai.google.dev/docs](https://ai.google.dev/docs)
    *   *Usage:* API reference for `gemini-pro`, prompt design best practices, and JSON mode.
4.  **Tailwind CSS v4**: [https://tailwindcss.com/docs](https://tailwindcss.com/docs)
    *   *Usage:* Utility class references and configuration.
5.  **SQLAlchemy 2.0**: [https://docs.sqlalchemy.org/en/20/](https://docs.sqlalchemy.org/en/20/)
    *   *Usage:* ORM relationship definitions and async session management.
6.  **Docker Documentation**: [https://docs.docker.com/](https://docs.docker.com/)
    *   *Usage:* Best practices for Python and Node.js containerization.

## 4. Conclusion

The Question Bank Generator project successfully demonstrates the power of combining modern web frameworks (Svelte, FastAPI) with cutting-edge AI (Gemini). The resulting application is robust, scalable, and provides genuine utility for educators and learners. The 8-week development cycle allowed for a structured approach to tackling the complexities of AI integration and real-time state management.
