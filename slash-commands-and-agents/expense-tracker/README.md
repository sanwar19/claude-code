# Expense Tracker

A simple command-line expense tracker application for managing personal expenses with category-based organisation and spending analysis.

## Features

- **Add Expenses**: Record expenses with amount, category, and description
- **View All Expenses**: Display expenses in a formatted table with timestamps
- **Total Spending**: Calculate and display total spending across all expenses
- **Category Analysis**: View spending breakdown by category with percentages
- **Filter by Category**: View expenses filtered by specific categories
- **Data Persistence**: Automatic saving and loading from JSON file
- **Input Validation**: Ensures valid amounts and prevents empty fields

## Installation

### Prerequisites

- Python 3.7 or higher (uses standard library only)

### Setup

1. Clone or download the project:
```bash
git clone <repository-url>
cd expense-tracker
```

2. Run the application (no additional dependencies required):
```bash
python expense_tracker.py
```

## Usage

### Running the CLI

Start the expense tracker by running:

```bash
python expense_tracker.py
```

You'll see the main menu with six options:

```
Welcome to the Expense Tracker!

Select an option:
1. Add expense
2. View all expenses
3. Calculate total spending
4. View spending by category
5. Filter expenses by category
6. Quit
```

### Example Usage Session

```bash
$ python expense_tracker.py

Welcome to the Expense Tracker!

Select an option:
1. Add expense
2. View all expenses
3. Calculate total spending
4. View spending by category
5. Filter expenses by category
6. Quit

Enter your choice (1-6): 1
Enter amount: £25.50
Enter category: Food
Enter description: Lunch at cafe
Expense added: £25.50 for Food

Enter your choice (1-6): 2
================================================================================
ID   Date                 Category        Amount     Description
================================================================================
1    2024-10-09 16:30:45  Food            £25.50     Lunch at cafe
================================================================================
```

### Programmatic Usage

You can use the module's classes directly in your own Python scripts:

```python
from expense_tracker import ExpenseTracker, JsonFileStorage

# Wire up storage and tracker
storage = JsonFileStorage("my_expenses.json")
tracker = ExpenseTracker(storage)

# Add expenses (returns True on success, False on validation failure)
tracker.add_expense(25.50, "Food", "Lunch at cafe")
tracker.add_expense(60.00, "Transport", "Monthly bus pass")

# Query data
total = tracker.calculate_total_spending()
print(f"Total spent: £{total:.2f}")

by_category = tracker.get_spending_by_category()
food_expenses = tracker.filter_by_category("Food")
categories = tracker.get_available_categories()
```

## API Reference

The module is organised into seven classes with clear responsibilities.

### `Expense` (dataclass)

Represents a single expense entry.

| Method | Signature | Description |
|--------|-----------|-------------|
| `create_new` | `(amount, category, description) -> Expense` | Factory — generates UUID and timestamp |
| `to_dict` | `() -> Dict[str, Any]` | Serialise to JSON-compatible dict |
| `from_dict` | `(data: Dict) -> Expense` | Deserialise from dict |

### `InputValidator`

Static validation helpers used by `ExpenseTracker`.

| Method | Signature | Description |
|--------|-----------|-------------|
| `validate_amount` | `(amount: float) -> bool` | True if amount > 0 |
| `validate_text_field` | `(text: str) -> bool` | True if non-empty and ≤ 100 chars |
| `validate_choice` | `(choice, min_val, max_val) -> bool` | True if within range |

### `DataStorage` (ABC)

Abstract base class for storage backends. Implement `save_expenses` and `load_expenses` to add a new backend.

### `JsonFileStorage(DataStorage)`

JSON file persistence implementation.

#### `__init__(file_path: str = "expenses.json") -> None`

**Parameters:**
- `file_path` (str): Path to the JSON data file (created automatically)

#### `save_expenses(expenses: List[Expense]) -> None` / `load_expenses() -> List[Expense]`

Persist and restore the full expense list. Handles missing files and JSON errors gracefully.

### `ExpenseTracker`

Business logic layer. All display-related concerns are delegated to `ExpenseTrackerUI`.

#### `__init__(storage: DataStorage) -> None`

**Parameters:**
- `storage`: Any `DataStorage` implementation

#### `add_expense(amount, category, description) -> bool`

Returns `True` if the expense was added, `False` if validation failed.

#### `get_all_expenses() -> List[Expense]`

Returns a copy of the internal expense list.

#### `calculate_total_spending() -> float`

Returns the sum of all expense amounts.

#### `get_spending_by_category() -> Dict[str, float]`

Returns a dict mapping each category name to its total amount.

#### `filter_by_category(category: str) -> List[Expense]`

Case-insensitive filter. Returns matching expenses.

#### `get_available_categories() -> List[str]`

Returns a sorted list of unique category names.

### `TableFormatter`

Static display helpers for rendering expense data to stdout.

| Method | Description |
|--------|-------------|
| `print_expenses_table(expenses)` | Full table with ID, date, category, amount, description |
| `print_category_breakdown(category_totals)` | Category totals with percentages |
| `print_category_expenses(expenses, category)` | Filtered view with per-category total |

### `ExpenseTrackerUI`

CLI interaction layer. Owns all user prompts and output formatting.

#### `__init__(tracker: ExpenseTracker) -> None`

#### `run() -> None`

Enters the main menu loop. Exits cleanly on choice `6` or `Ctrl+C`.

## File Structure

```
expense-tracker/
├── expense_tracker.py        # CLI application (core module)
├── test_expense_tracker.py   # Unit tests (37 tests, stdlib unittest)
├── app/                      # FastAPI REST API layer
│   ├── main.py               # App setup, middleware, exception handlers
│   ├── config.py             # Settings and environment configuration
│   ├── api/v1/endpoints/     # Route handlers
│   ├── services/             # Business logic wrapping ExpenseTracker
│   ├── schemas/              # Pydantic request/response models
│   └── middleware/           # Error handling middleware
├── expenses.json             # Data storage (created automatically)
├── requirements-api.txt      # Dependencies for the FastAPI layer
└── README.md                 # This file
```

## Data Storage

Expenses are stored in JSON format with the following structure:

```json
[
  {
    "id": "3f2a1b4c-...",
    "amount": 25.50,
    "category": "Food",
    "description": "Lunch at cafe",
    "date": "2024-10-09 16:30:45"
  }
]
```

`id` is a UUID4 string generated automatically by `Expense.create_new()`.

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Make your changes
4. Add tests if applicable
5. Commit your changes (`git commit -am 'Add new feature'`)
6. Push to the branch (`git push origin feature/new-feature`)
7. Create a Pull Request

## Development

### Code Style

- Follows PEP 8 standards
- Uses type hints for all function parameters and returns
- Comprehensive docstrings for all public methods
- UK English spelling conventions

### Testing

The project includes comprehensive unit tests covering all major components.

#### Running Tests

Run the test suite using Python's unittest module:

```bash
# Run all tests with verbose output
python3 -m unittest test_expense_tracker -v

# Run specific test class
python3 -m unittest test_expense_tracker.TestExpenseTracker -v

# Run specific test method
python3 -m unittest test_expense_tracker.TestExpenseTracker.test_add_expense_valid -v
```

#### Test Coverage

The test suite includes 37 tests covering:

- **Expense class**: Creation, serialisation, and deserialisation
- **InputValidator**: Validation of amounts, text fields, and menu choices
- **JsonFileStorage**: Saving and loading data, error handling
- **ExpenseTracker**: All CRUD operations and business logic
- **TableFormatter**: All display and formatting functions
- **ExpenseTrackerUI**: User interface interactions and input handling

All tests are passing with 100% success rate.

#### Manual Testing

You can also manually test the application:

```bash
# Test adding expenses
python3 expense_tracker.py

# Test with different categories and amounts
# Verify data persistence by restarting the application
```

## License

This project is open source. Feel free to use, modify, and distribute.

## Changelog

### v1.1.0
- Moved success print from `ExpenseTracker.add_expense()` to `ExpenseTrackerUI` — business logic no longer writes to stdout
- Removed redundant amount validation in UI (handled by `InputValidator` inside the tracker)
- Updated API Reference in README to match actual class hierarchy

### v1.0.0
- Initial release with core expense tracking functionality
- CLI interface with 6 main options
- JSON-based data persistence
- Category-based expense organisation
- Spending analysis and filtering capabilities