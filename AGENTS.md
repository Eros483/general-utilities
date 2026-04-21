# AGENT.md

## Project Overview
[2-3 sentences: what this project does, who it's for, and what problem it solves]

## Tech Stack
- Frontend: React + Vite
- Backend: FastAPI
- Database: SQLite + SQLAlchemy
- Styling: [TailwindCSS / shadcn / etc — fill per project]
- State Management: [Zustand / Redux — fill per project]

## Key Commands

### Backend
```bash
cd backend
uvicorn main:app --reload        # dev server
pytest                           # run all tests
pytest tests/test_api/           # run api tests only
black .                          # format code
```

### Frontend
```bash
cd frontend
npm run dev                      # dev server
npm run build                    # production build
npm run test                     # run tests
npm run lint                     # lint
```

## Directory Structure

```
project-name/
├── frontend/
│   ├── src/
│   │   ├── components/          # reusable UI components
│   │   ├── pages/               # route-level components
│   │   ├── hooks/               # custom React hooks
│   │   ├── utils/               # helper functions
│   │   ├── assets/              # images, fonts, static files
│   │   ├── store/               # state management
│   │   ├── services/            # API call functions (fetch/axios wrappers)
│   │   └── main.jsx             # entry point
│   ├── public/
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
│
├── backend/
│   ├── core/                    # business logic, domain layer (no HTTP knowledge)
│   ├── api/
│   │   └── v1/                  # versioned route handlers (thin layer)
│   ├── models/                  # SQLAlchemy DB models
│   ├── schemas/                 # Pydantic schemas (request/response)
│   ├── utils/
│   │   ├── config.py            # Pydantic BaseSettings class, instantiated as `config`
│   │   ├── logger.py            # custom logger, imported as `logger`
│   │   └── [other helpers]
│   ├── tests/
│   │   ├── test_api/            # mirrors api/v1/ structure
│   │   └── test_core/           # mirrors core/ structure
│   ├── main.py                  # FastAPI app entry point
│   ├── config.py                # settings and env vars
│   └── requirements.txt
│
├── docs/
│   ├── features.json            # canonical feature tracker — always kept up to date
│   └── [design docs]           # per-feature or per-module design documents
├── .env.example                 # committed, no secrets
├── .gitignore
├── README.md
└── AGENT.md
```

## Conventions

### Python (Backend)
- Every backend file starts with a header comment: `# ----- <4-5 word purpose> @ <file location> -----`
  - Example: `# ----- user authentication logic @ backend/core/auth.py -----`
- Formatter: black (always)
- Naming: snake_case for everything — files, variables, functions, DB columns
- Imports: sorted (isort compatible with black)
- API routes are thin: validate input → call core → return output
- core/ has zero knowledge of HTTP or FastAPI
- Env vars are accessed exclusively via the config object (`from utils.config import config`) — never use `os.environ` directly
- Config is a Pydantic BaseSettings class instantiated once in `backend/utils/config.py`
- All logging uses the custom logger (`from utils.logger import logger`) — never use `print` or the stdlib `logging` module directly

### JavaScript (Frontend)
- camelCase for variables and functions
- PascalCase for components and types
- snake_case for file names
- All backend API calls go through services/, never directly in components

### General
- Commits: conventional commits format (feat:, fix:, chore:, docs:, test:, refactor:)
- Env vars: never committed, always have a .env.example with keys but no values
- API versioned from day one under /api/v1/

## Development Philosophy
- TDD first: write the test, then the implementation. Never skip.
- Tests mirror the structure of the module they test
- No function ships without a test
- API routes are thin — logic lives in core/
- Explicit over clever — readable code beats smart code

## Agent Roles

This project uses a three-agent workflow. Every task goes through all three stages.

- **Planner**: breaks down the task, identifies edge cases and risks, defines what tests need to exist, produces a written plan. Writes no code. Must check /docs for any relevant design documents before planning.
- **Builder**: implements exactly per the plan — no scope creep, no improvising. Writes tests first, then implementation.
- **Reviewer**: checks correctness, black formatting, snake_case compliance, test coverage, and edge cases. Flags anything that deviates from this AGENT.md. Verifies that docs/features.json has been updated to reflect the work done.

The Planner must finish before the Builder starts.
The Reviewer must approve before any task is considered done.

## Agent Guidelines
- Always run black before considering Python code done
- Always use snake_case — no exceptions for Python files, variables, functions, DB columns
- Never modify files in /docs unless explicitly asked
- Always run tests after making changes — if tests fail, fix before moving on
- Every new backend file must start with the header comment — Reviewer should flag any file missing it
- Never use `os.environ` directly — always use `from utils.config import config`
- Never use `print` or stdlib `logging` — always use `from utils.logger import logger`
- Never put API calls directly in React components — they belong in services/
- Always check /docs for relevant design documents before starting any task — if a design doc exists for what you're building, it takes precedence
- If a design doc is missing but the task is significant enough to warrant one, flag it to the user before proceeding
- Always update docs/features.json after completing any task — mark features as done, update test status, add new features if they were introduced. follow the schema shape provided

````bash
// docs/features.json
{
  "project": "[project-name]",
  "last_updated": "YYYY-MM-DD",
  "summary": {
    "total": 0,
    "completed": 0,
    "in_progress": 0,
    "planned": 0,
    "tests_passing": 0,
    "tests_failing": 0,
    "tests_missing": 0
  },
  "features": [
    {
      "id": "F001",
      "name": "[Feature Name]",
      "description": "[What it does and why it exists]",
      "status": "planned",
      "priority": "high",
      "module": "backend/core",
      "design_doc": "docs/[relevant-design-doc].md",
      "tests": {
        "status": "missing",
        "files": [],
        "notes": ""
      },
      "subtasks": [
        {
          "id": "F001-1",
          "name": "[Subtask name]",
          "status": "planned"
        }
      ],
      "notes": "",
      "added": "YYYY-MM-DD",
      "completed": null
    }
  ]
}
````

- If something feels out of scope, flag it rather than silently doing it

## Project-Specific Notes
[Fill this in per project:]
- External APIs used and where keys are stored
- Any non-standard setup steps
- Files or directories that should never be touched
- Deployment target and process
- Known gotchas or quirks