# Portfolio workspace

This repository contains local development orchestration for the three portfolio applications:

- `portfolio-frontend`: React and Vite
- `portfolio-backend`: Spring Boot
- `portfolio-ai`: FastAPI and Groq

The application directories remain independent Git repositories. This repository only tracks shared workspace files.

## Requirements

- Docker with Docker Compose
- The three application repositories cloned as sibling directories shown above

## Setup

1. Copy `portfolio-ai/.env.example` to `portfolio-ai/.env`.
2. Set `GROQ_API_KEY` in `portfolio-ai/.env`.
3. Configure the Supabase JDBC values and `GITHUB_API_TOKEN` in `portfolio-backend/.env`.
4. Start the development stack:

   ```bash
   docker compose up
   ```

The services are available at:

- Frontend: <http://localhost:5176/portfolio-frontend/>
- Backend: <http://localhost:8080/actuator/health>
- AI API: <http://localhost:8000/health>

Source directories are bind-mounted into their containers. Vite and Uvicorn reload automatically. Restart the backend service after Java source changes:

```bash
docker compose restart backend
```

Stop the stack with:

```bash
docker compose down
```

Dependency caches live in Docker named volumes, not in this repository.

The AI container receives the private job master through a read-only bind mount. GitHub metadata and README retrieval is owned by the Java backend; Python consumes only the backend's sanitized knowledge feed.
