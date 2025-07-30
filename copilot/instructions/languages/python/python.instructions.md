---
applyTo: '**/*.py'
---
# Python Instructions

- **CRITICAL**: You MUST ALWAYS read and follow ALL instructions from ALL files from:
    - [./python-standards.md](./python-standards.md)

## Implementation Requirements

When implementing any Python-related functionality:

- You must have read the complete Python documentation before proceeding
- You must adhere to all guidelines provided in the Python documentation
- You must implement all instructions exactly as specified
- You must follow PEP 8 style guide

## Table of Contents

- [Project Structure & Management](#project-structure--management)
  - [Standard Project Layout](#standard-project-layout)
  - [Python Project Internal Structure](#python-project-internal-structure)
  - [Managing Python Projects](#managing-python-projects)
- [Keeping Up-to-Date](#keeping-up-to-date)
  - [Latest Python Versions](#latest-python-versions)

## Project Structure & Management

### Standard Project Layout

- `pyproject.toml`: Modern Python project configuration
- `requirements.txt` or `requirements/`: Dependencies
- `src/`: Source code directory
- `tests/`: Test files
- `.env`: Environment variables (in .gitignore)
- `README.md`: Project documentation

#### Folder Layout Example

```plaintext
pyproject.toml
requirements.txt
src/
  package_name/
    __init__.py
    main.py
    core/
      __init__.py
      models.py
    utils/
      __init__.py
      helpers.py
tests/
  __init__.py
  test_models.py
  test_helpers.py
.env
README.md
```

### Python Project Internal Structure

Prefer simple project folder structures. ALWAYS follow existing project conventions. For new projects:

- `src/package_name/`: Main package directory
  - `__init__.py`: Package initialization
  - `main.py`: Entry point
  - `core/`: Core functionality
  - `models/`: Data models and schemas
  - `utils/`: Utility functions
  - `services/`: Business logic and external services
  - `api/`: API endpoints (if applicable)
  - `db/`: Database operations
  - `config/`: Configuration management
  - `templates/`: Templates (if applicable)
  - `static/`: Static files (if applicable)

### Managing Python Projects

ALWAYS use modern Python tools:

- **Project Setup**:
  - Use `python -m venv .venv` for virtual environments
  - Activate: 
    - Windows: `.venv\\Scripts\\activate`
    - Unix: `source .venv/bin/activate`
  - Modern project setup: `python -m pip install poetry`

- **Dependencies**:
  ```toml
  # pyproject.toml
  [tool.poetry]
  name = "project-name"
  version = "0.1.0"
  description = ""
  authors = ["Your Name <your.email@example.com>"]

  [tool.poetry.dependencies]
  python = "^3.12"

  [tool.poetry.group.dev.dependencies]
  pytest = "^7.4"
  black = "^23.7"
  mypy = "^1.5"
  ruff = "^0.1"
  ```

- **Development Tools**:
  - Formatting: `black .`
  - Linting: `ruff check .`
  - Type checking: `mypy .`
  - Testing: `pytest`

- **Package Installation**:
  - With Poetry: `poetry add package-name`
  - With pip: `python -m pip install package-name`
  - Dev dependencies: `poetry add --group dev package-name`

- **Environment Management**:
  - Create requirements: `poetry export -f requirements.txt --output requirements.txt`
  - Install requirements: `python -m pip install -r requirements.txt`
  - Lock dependencies: `poetry lock`

## Keeping Up-to-Date

### Latest Python Versions

ALWAYS prefer the latest Python version unless specified otherwise. Current stable versions:

- Python 3.12: Latest stable release
  - Pattern matching improvements
  - Better error messages
  - Performance improvements
  - Type hint improvements

- Python 3.11:
  - Exception groups
  - Task groups in asyncio
  - Enhanced error locations
  - Improved performance

Key features to use:
- Type hints (PEP 484, 563, 585, 604)
- Pattern matching (PEP 634)
- Dataclasses (PEP 557)
- F-strings (PEP 498)
- Async/await (PEP 492)
- Walrus operator (PEP 572)
- Structural pattern matching (PEP 634)

### Type Checking

ALWAYS use type hints in new code:

```python
from typing import Optional, List, Dict, TypeVar, Generic

T = TypeVar("T")

class Repository(Generic[T]):
    def get_by_id(self, id: str) -> Optional[T]:
        ...

    def save(self, item: T) -> None:
        ...

    def get_all(self) -> List[T]:
        ...
```

### Testing

ALWAYS write tests for new code using pytest:

```python
import pytest
from your_package.core import process_data

def test_process_data_valid():
    result = process_data({"key": "value"})
    assert result.status == "success"

def test_process_data_invalid():
    with pytest.raises(ValueError):
        process_data(None)
```
