# Todo FastAPI CRUD

This small FastAPI project provides a simple file-backed TODO CRUD API.

## Project structure

- `main.py` - application entry (FastAPI app, includes router)
- `routes/TodoRoute.py` - API routes and file read/write logic
- `models/Todo.py` - Pydantic model for request validation
- `db.json` - local JSON file used as a simple datastore
- `requirements.txt` - Python dependencies

## Prerequisites

- Python 3.10+ (or compatible)
- Git (optional)

## Setup (Windows PowerShell)

1. Create and activate a virtual environment (if you don't have one):

```powershell
python -m venv venv
& .\venv\Scripts\Activate.ps1
```

2. Install dependencies:

```powershell
pip install -r requirements.txt
```

## Run the app (development)

Start the FastAPI server using Uvicorn from the project root:

```powershell
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Then open http://127.0.0.1:8000 in your browser. The automatic docs are available at:

- Swagger UI: http://127.0.0.1:8000/docs
- ReDoc: http://127.0.0.1:8000/redoc

## How it works — step by step

- `main.py`:
  - Creates the FastAPI instance and includes the router from `routes/TodoRoute.py`.
  - Adds a simple root endpoint (`GET /`) that returns a running message.

- `models/Todo.py`:
  - Defines the `Todo` Pydantic model with fields:
    - `title` (string, 3–100 chars)
    - `desc` (string, min 3 chars)
    - `isComplete` (optional bool, defaults to `False`)
  - This model is used by the create endpoint to validate incoming JSON.

- `routes/TodoRoute.py`:
  - Loads `db.json` into memory at startup via `load_file()`.
  - Keeps `todos` in a module-level list and writes changes back with `save_file()`.
  - Endpoints:
    - `POST /api/v1/create` — create a new todo (returns 201).
    - `GET  /api/v1/` — returns the list of todos.
    - `GET  /api/v1/{id}` — returns a single todo by id, or 404 if not found.
  - New items get an `id` assigned as `len(todos) + 1`.

- `db.json`:
  - A plain JSON array storing todo objects. The app rewrites this file on every create.
  - Keep in mind this approach is fine for demos and local development but not for production concurrency.

## API usage examples

- Get server status:

```bash
curl http://127.0.0.1:8000/
```

- Get all todos:

```bash
curl http://127.0.0.1:8000/api/v1/
```

- Get a todo by id (example id=1):

```bash
curl http://127.0.0.1:8000/api/v1/1
```

- Create a todo (example):

```bash
curl -X POST http://127.0.0.1:8000/api/v1/create \
  -H "Content-Type: application/json" \
  -d '{"title":"Buy milk","desc":"Buy 2 liters","isComplete":false}'
```

Request body must follow the `Todo` model (see `models/Todo.py`) — invalid data returns a 422 validation error.

## Notes & next steps

- Data persistence: `db.json` is overwritten on each create; consider using a real database (SQLite/Postgres) for persistence and concurrency.
- Error handling: only basic 404 handling exists for `GET /{id}` — you can add update/delete endpoints.
- Security: enable CORS, authentication, and input sanitization before exposing publicly.

## Contact

If you want, I can:

- Add update/delete endpoints.
- Migrate storage to SQLite.
- Add tests and CI configuration.
