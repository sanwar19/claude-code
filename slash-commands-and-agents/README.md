# Claude Code — Slash Commands & Agents

A practical reference project demonstrating how to build and use **Claude Code slash commands** and **sub-agents** in a real Python project (an expense tracker CLI + REST API).

## Repository Structure

```
slash-commands-and-agents/
├── README.md                        # This file
├── expense-tracker/                 # Main example project
│   ├── expense_tracker.py           # CLI application
│   ├── test_expense_tracker.py      # Unit tests (37 tests)
│   ├── requirements-api.txt         # FastAPI dependencies
│   ├── app/                         # REST API (FastAPI)
│   │   ├── main.py                  # FastAPI app entry point
│   │   ├── config.py                # Settings & environment config
│   │   ├── api/v1/                  # API v1 routes
│   │   ├── schemas/                 # Pydantic request/response models
│   │   ├── services/                # Business logic
│   │   ├── middleware/              # Error handling, security headers
│   │   └── utils/                  # Shared utilities
│   ├── README.md                    # Expense tracker usage docs
│   ├── API.md                       # REST API reference
│   └── CONTRIBUTING.md             # Contribution guidelines
└── .claude/
    ├── agents/
    │   └── api-reviewer.md          # Custom API review sub-agent
    └── commands/
        ├── expense-tracker-workflow.md       # Full refactor + doc workflow
        ├── expense-tracker-code-refactor.md  # Code refactoring
        ├── expense-tracker-doc.md            # Documentation generation
        ├── expense-tracker-git-commit.md     # Conventional commit helper
        ├── api-review-and-commit-workflow.md # API review + commit pipeline
        ├── feature-dev.md                    # Feature development guide
        ├── fix-issue.md                      # Issue fixing workflow
        └── review-pr.md                      # PR review command
```

## Slash Commands

Run any command from within this project using Claude Code. Commands are defined in `.claude/commands/`.

| Command | Description |
|---|---|
| `/expense-tracker-workflow [file]` | Full pipeline: refactor code then generate documentation |
| `/expense-tracker-code-refactor [file]` | Refactor Python code for better quality |
| `/expense-tracker-doc` | Generate all documentation |
| `/expense-tracker-doc --readme` | Generate or update README |
| `/expense-tracker-doc --api` | Generate API endpoint documentation |
| `/expense-tracker-doc --check` | Show documentation coverage report |
| `/expense-tracker-git-commit` | Create a conventional git commit for staged changes |
| `/api-review-and-commit-workflow` | Review API design then commit |
| `/feature-dev` | Scaffold a new feature |
| `/fix-issue` | Diagnose and fix a reported issue |
| `/review-pr` | Review a pull request |

## Sub-Agents

Custom agents are defined in `.claude/agents/` and are invoked automatically by slash commands or can be called directly.

| Agent | Description |
|---|---|
| `api-reviewer` | Reviews API endpoints, schemas, and docs for REST best practices, security, and usability |

## Expense Tracker — Quick Start

### CLI Application

Requires Python 3.7+. No extra dependencies.

```bash
cd expense-tracker
python expense_tracker.py
```

Menu options:

```
1. Add expense
2. View all expenses
3. Calculate total spending
4. View spending by category
5. Filter expenses by category
6. Quit
```

### REST API

```bash
cd expense-tracker
pip install -r requirements-api.txt
uvicorn app.main:app --reload
```

Interactive docs available at `http://localhost:8000/api/docs`.

### Run Tests

```bash
cd expense-tracker
python -m unittest test_expense_tracker -v
```

37 tests covering `Expense`, `InputValidator`, `JsonFileStorage`, `ExpenseTracker`, `TableFormatter`, and `ExpenseTrackerUI`.

## Expense Tracker — Key Classes

| Class | Role |
|---|---|
| `Expense` | Dataclass representing a single expense (UUID, amount, category, description, date) |
| `InputValidator` | Validates amounts, text fields, and menu choices |
| `JsonFileStorage` | Persists expenses to `expenses.json` |
| `ExpenseTracker` | Core business logic — add, query, filter, and summarise expenses |
| `TableFormatter` | Formats tabular output for the CLI |
| `ExpenseTrackerUI` | Interactive CLI menu loop |

### Programmatic Usage

```python
from expense_tracker import ExpenseTracker, JsonFileStorage

storage = JsonFileStorage("my_expenses.json")
tracker = ExpenseTracker(storage)

tracker.add_expense(25.50, "Food", "Lunch at cafe")
tracker.add_expense(60.00, "Transport", "Monthly bus pass")

print(tracker.calculate_total_spending())    # 85.5
print(tracker.get_spending_by_category())    # {'Food': 25.5, 'Transport': 60.0}
```

## REST API — Key Endpoints

Base URL: `http://localhost:8000/api/v1`

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/expenses` | List expenses (paginated, filterable, sortable) |
| `POST` | `/expenses` | Create a new expense |
| `GET` | `/expenses/{id}` | Get a single expense |
| `PUT` | `/expenses/{id}` | Replace an expense |
| `PATCH` | `/expenses/{id}` | Partially update an expense |
| `DELETE` | `/expenses/{id}` | Delete an expense |
| `GET` | `/expenses/summary` | Spending analytics by category |
| `GET` | `/expenses/categories` | List all categories with totals |
| `GET` | `/health` | API health check |

See `expense-tracker/API.md` for the full reference with request/response examples.

## Contributing

See `expense-tracker/CONTRIBUTING.md` for development setup, code style (PEP 8, UK English), and the PR workflow.

## License

Open source. Free to use, modify, and distribute.
