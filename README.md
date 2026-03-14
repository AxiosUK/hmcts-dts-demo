# HMCTS DTS Case Worker Task Demo

This is the instruction repo for the complete HMCTS DTS Case Worker Task Demo.

## Frontend Summary

Overview

- Lightweight task management UI with a List view (DataGrid) and a Kanban-style board.
- Built with Material UI and MUI X Data Grid; `dayjs` provides date handling and relative formatting.
- Includes in-memory demo data and an API client with safe fallbacks to make local development resilient when backend services are unavailable.

Key features

- List view: sorting, pagination, column visibility and quick search.
- Kanban board: drag-and-drop status updates and compact cards.
- Create / view / edit tasks in a modal dialog with date-time selection.
- Status chips and human-friendly relative due dates.

Tech stack

- React + TypeScript, built with Vite
- Material UI (`@mui/material`, `@mui/icons-material`)
- MUI X Data Grid (`@mui/x-data-grid`) and Date Pickers (`@mui/x-date-pickers`)
- Axios for HTTP, Dayjs for date utilities

More information about the frontend package can be found here:
https://github.com/AxiosUK/dts-frontend

## API Summary

This service exposes a small REST API under the versioned path `/main/v1`.

- Health endpoints:
  - `GET /healthcheck` — returns plain `OK`.
  - `GET /status` — returns JSON status and timestamp.

- Tasks resource (mounted at `/main/v1/tasks`):
  - `GET /main/v1/tasks` — list tasks with filtering, sorting, field selection and pagination.
  - `GET /main/v1/tasks/:id` — get a task by id.
  - `POST /main/v1/tasks` — create a new task.
  - `PATCH /main/v1/tasks/:id` — update a task.
  - `DELETE /main/v1/tasks/:id` — delete a task.

More information about the backend package can be found here:
https://github.com/AxiosUK/dts-backend

## Docker Compose for the whole package

```yaml
services:
  mongodb:
    image: mongodb/mongodb-community-server:latest
    restart: unless-stopped
    ports:
      - 27017:27017
    environment:
      - MONGODB_INITDB_ROOT_USERNAME=axios
      - MONGODB_INITDB_ROOT_PASSWORD=AxiosDemo
    volumes:
      - mongodb_data:/data/db

  axios-dts-backend:
    image: ghcr.io/axiosuk/dts-backend:latest
    restart: unless-stopped
    depends_on:
      - mongodb
    ports:
      - "5180:5180"
    environment:
      - NODE_ENV=production
      - DATABASE=mongodb://mongodb:27017/axios-dts-backend?authSource=admin
      - DATABASE_USERNAME=axios
      - DATABASE_PASSWORD=AxiosDemo
      - ALLOWED_HOSTS=http://localhost:5173
      - TRUST_PROXY=true
      - RATE_LIMIT_MAX=200

  axios-dts-frontend:
    image: ghcr.io/axiosuk/dts-frontend:latest
    environment:
      - API_BASE_URL=https://api.example.com
    restart: unless-stopped
    ports:
      - "5173:80"

volumes:
  mongodb_data:
```
