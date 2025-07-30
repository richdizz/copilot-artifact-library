# Python Coding Standards

## Code Conventions and Styles

CRITICAL: ALWAYS follow these conventions unless specified otherwise or if existing code differs:

- **PEP 8**: Follow PEP 8 style guide
- **Naming**:
  - Modules: `lowercase.py`
  - Packages: `lowercase`
  - Classes: `PascalCase`
  - Functions: `snake_case`
  - Variables: `snake_case`
  - Constants: `UPPER_SNAKE_CASE`
  - Protected: Single leading underscore `_protected`
  - Private: Double leading underscore `__private`

### String Formatting

- Prefer f-strings for string interpolation
- Use triple quotes for docstrings
- Use double quotes for string literals
- Use single quotes for string literals within double quotes

### Imports

Order imports as follows:
1. Standard library imports
2. Related third party imports
3. Local application/library specific imports

Group imports with a single blank line between groups:

```python
import os
import sys
from typing import Optional, List

import numpy as np
import pandas as pd

from .models import User
from .utils import format_date
```

### Type Hints

ALWAYS use type hints in new code:

```python
from typing import Dict, List, Optional, Sequence, TypeVar

T = TypeVar("T")

def process_items(items: Sequence[T]) -> List[T]:
    """Process a sequence of items and return processed list."""
    return [item for item in items if validate_item(item)]

def get_user(user_id: str) -> Optional[Dict[str, str]]:
    """Retrieve user information by ID."""
    return db.get_user(user_id)
```

### Classes

Follow these patterns for class definitions:

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class Config:
    """Configuration class for application settings."""
    
    host: str
    port: int
    debug: bool = False
    timeout: Optional[int] = None

class Service:
    """Service class for handling business logic."""

    def __init__(self, config: Config) -> None:
        """Initialize the service with configuration.
        
        Args:
            config: Configuration instance
        """
        self._config = config
        self._initialized = False

    @property
    def is_initialized(self) -> bool:
        """Check if service is initialized."""
        return self._initialized

    def initialize(self) -> None:
        """Initialize the service."""
        if self._initialized:
            raise RuntimeError("Service already initialized")
        
        self._initialized = True

    def _internal_method(self) -> None:
        """Internal helper method."""
        pass
```

### Error Handling

ALWAYS use specific exceptions and proper error handling:

```python
class ServiceError(Exception):
    """Base exception for service errors."""

class ValidationError(ServiceError):
    """Raised when validation fails."""
    
    def __init__(self, message: str, field: str) -> None:
        self.field = field
        super().__init__(f"{message} (field: {field})")

def process_data(data: Dict[str, str]) -> None:
    """Process the input data.
    
    Args:
        data: Input data dictionary
        
    Raises:
        ValidationError: If data validation fails
        ServiceError: If processing fails
    """
    try:
        if not validate_data(data):
            raise ValidationError("Invalid data", "unknown")
        
        result = perform_processing(data)
        save_result(result)
    except DatabaseError as e:
        raise ServiceError(f"Database operation failed: {e}") from e
    except Exception as e:
        raise ServiceError(f"Unexpected error: {e}") from e
```

### Async Code

ALWAYS use async/await properly:

```python
import asyncio
from typing import List, Optional

class AsyncService:
    """Service with async operations."""

    async def fetch_data(self, item_id: str) -> Optional[dict]:
        """Fetch data asynchronously.
        
        Args:
            item_id: ID of the item to fetch
            
        Returns:
            Dict containing item data or None if not found
        """
        try:
            async with self.session.get(f"/items/{item_id}") as response:
                if response.status == 404:
                    return None
                response.raise_for_status()
                return await response.json()
        except aiohttp.ClientError as e:
            raise ServiceError(f"Failed to fetch data: {e}") from e

    async def process_items(self, items: List[str]) -> List[dict]:
        """Process multiple items concurrently.
        
        Args:
            items: List of item IDs to process
            
        Returns:
            List of processed items
        """
        tasks = [self.fetch_data(item) for item in items]
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        processed = []
        for result in results:
            if isinstance(result, Exception):
                logger.error(f"Failed to process item: {result}")
            elif result is not None:
                processed.append(result)
        
        return processed
```

## Documentation Standards

ALWAYS use proper docstrings (Google style):

```python
from typing import Optional, Tuple

class DataProcessor:
    """A class for processing data with various transformations.
    
    This class provides methods for loading, transforming, and saving data
    with support for various formats and validation rules.
    
    Attributes:
        source_path: Path to the source data
        config: Configuration dictionary for processing
    """

    def __init__(self, source_path: str, config: Optional[dict] = None) -> None:
        """Initialize the DataProcessor.
        
        Args:
            source_path: Path to the source data file
            config: Optional configuration dictionary
            
        Raises:
            ValueError: If source_path doesn't exist
        """
        self.source_path = source_path
        self.config = config or {}

    def process_file(self) -> Tuple[int, int]:
        """Process the source file and return statistics.
        
        Processes the file at source_path according to the configuration
        and returns statistics about the processing.
        
        Returns:
            A tuple containing (processed_count, error_count)
            
        Raises:
            FileNotFoundError: If source file is missing
            ProcessingError: If processing fails
        """
        processed = 0
        errors = 0
        
        # Processing logic here
        
        return processed, errors
```

## Testing Standards

ALWAYS write tests for new code:

```python
import pytest
from unittest.mock import Mock, patch

from your_package.processor import DataProcessor
from your_package.exceptions import ProcessingError

@pytest.fixture
def processor():
    """Create a DataProcessor instance for testing."""
    return DataProcessor("test.txt", {"validate": True})

def test_process_file_success(processor):
    """Test successful file processing."""
    # Arrange
    expected = (10, 0)
    
    # Act
    result = processor.process_file()
    
    # Assert
    assert result == expected

def test_process_file_with_errors():
    """Test file processing with errors."""
    # Arrange
    processor = DataProcessor("test.txt", {"strict": True})
    
    # Act & Assert
    with pytest.raises(ProcessingError) as exc_info:
        processor.process_file()
    assert "Processing failed" in str(exc_info.value)

@pytest.mark.asyncio
async def test_async_processing():
    """Test async processing functionality."""
    # Arrange
    processor = DataProcessor("test.txt")
    
    # Act
    result = await processor.process_async()
    
    # Assert
    assert result.status == "success"
```

## Best Practices

1. **SOLID Principles**:
   - Single Responsibility: Each class/function does one thing
   - Open/Closed: Open for extension, closed for modification
   - Liskov Substitution: Subtypes must be substitutable
   - Interface Segregation: Specific interfaces over general
   - Dependency Inversion: Depend on abstractions

2. **Code Organization**:
   - Keep functions small and focused
   - Use meaningful names
   - Document complex logic
   - Use type hints
   - Follow the principle of least surprise

3. **Performance**:
   - Use appropriate data structures
   - Avoid premature optimization
   - Profile code when needed
   - Use generators for large datasets
   - Consider memory usage

4. **Security**:
   - Validate input data
   - Use secure defaults
   - Handle secrets properly
   - Follow security best practices
   - Regular dependency updates
