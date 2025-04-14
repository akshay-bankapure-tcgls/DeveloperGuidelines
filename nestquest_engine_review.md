# Code Review: NestQuest Engine Improvements

This document provides a comprehensive review of the NestQuest engine codebase, highlighting areas for improvement and best practices implementation.

---

## 1. **Bedrock Service Module**

### ❌ Issues Found:
1. **Error Handling**
```python
try:
    return boto3.client(...)
except Exception as e:
    logger.error(f"Failed to initialize Bedrock client: {str(e)}")
    raise
```

2. **Hardcoded Values**
```python
"anthropic_version": "bedrock-2023-05-31",
"max_tokens": 1000,
"temperature": 0.7,
```

3. **Magic Numbers**
```python
if current_section == "common" and len(common) < 3:
    common.append(description)
elif current_section == "differing" and len(differing) < 3:
    differing.append(description)
```

### ✅ Recommended Improvements:

1. **Better Error Handling**
```python
class BedrockServiceError(Exception):
    """Custom exception for Bedrock service errors."""
    pass

def _initialize_client(self) -> boto3.client:
    """Initialize AWS Bedrock client with proper error handling."""
    try:
        return boto3.client(
            service_name='bedrock-runtime',
            region_name=os.getenv('AWS_REGION', 'us-east-1'),
            aws_access_key_id=os.getenv('AWS_ACCESS_KEY_ICMS'),
            aws_secret_access_key=os.getenv('AWS_SECRET_KEY_ICMS')
        )
    except boto3.exceptions.Boto3Error as e:
        logger.error(f"Boto3 error initializing Bedrock client: {str(e)}")
        raise BedrockServiceError("Failed to initialize Bedrock client") from e
    except Exception as e:
        logger.error(f"Unexpected error initializing Bedrock client: {str(e)}")
        raise BedrockServiceError("Unexpected error initializing Bedrock client") from e
```

2. **Configuration Constants**
```python
class BedrockConfig:
    ANTHROPIC_VERSION = "bedrock-2023-05-31"
    MAX_TOKENS = 1000
    TEMPERATURE = 0.7
    MAX_CHARACTERISTICS = 3
```

3. **Type Safety**
```python
from typing import TypedDict

class CharacteristicResponse(TypedDict):
    common_characteristics: List[str]
    differing_characteristics: List[str]

def _parse_llm_response(self, response_text: str) -> CharacteristicResponse:
    """Parse LLM response into structured format with type safety."""
```

### 📝 Explanation:
- Current implementation lacks:
  - Proper error hierarchy
  - Configuration management
  - Type safety
  - Input validation
  - Retry mechanism for API calls
- Improvements needed:
  - Custom exception types
  - Configuration class
  - Type hints and validation
  - Retry logic for API calls
  - Better logging

---

## 2. **Characteristics Generator Module**

### ❌ Issues Found:

1. **Hardcoded Configuration**
```python
self.characteristic_templates = {
    "TIME_WEEKNIGHT": {
        "TIME_WEEKNIGHT_BEFORE_10": ("Early Sleeper", "likes to get to bed early"),
        # ... more hardcoded values
    }
}
```

2. **Magic Numbers**
```python
return common_characteristics[:3], differing_characteristics[:3]
```

3. **Limited Error Handling**
```python
except Exception as e:
    logger.error(f"Error processing preference {key}: {str(e)}")
    continue
```

### ✅ Recommended Improvements:

1. **Configuration Management**
```python
from dataclasses import dataclass
from typing import Dict, Tuple

@dataclass
class CharacteristicTemplate:
    display_name: str
    description: str
    emoji: str

class CharacteristicConfig:
    MAX_CHARACTERISTICS = 3
    CHARACTERISTICS = {
        "TIME_WEEKNIGHT": {
            "BEFORE_10": CharacteristicTemplate("Early Sleeper", "likes to get to bed early", "🌙"),
            # ... other templates
        }
    }
```

2. **Type Safety and Validation**
```python
from typing import TypedDict, List

class Preference(TypedDict):
    key: str
    value: str

class CharacteristicResult(TypedDict):
    common: List[str]
    differing: List[str]

def analyze_preferences(self, preferences_list: List[Preference]) -> CharacteristicResult:
    """Analyze preferences with proper type safety."""
```

3. **Better Error Handling**
```python
class CharacteristicError(Exception):
    """Base exception for characteristics generation errors."""
    pass

class InvalidPreferenceError(CharacteristicError):
    """Raised when a preference is invalid."""
    pass

def analyze_preferences(self, preferences_list: List[Dict[str, str]]) -> Tuple[List[str], List[str]]:
    try:
        # ... existing code ...
    except KeyError as e:
        logger.error(f"Missing required preference key: {str(e)}")
        raise InvalidPreferenceError(f"Missing preference key: {str(e)}") from e
    except Exception as e:
        logger.error(f"Unexpected error analyzing preferences: {str(e)}")
        raise CharacteristicError("Failed to analyze preferences") from e
```

### 📝 Explanation:
- Current implementation issues:
  - Hardcoded configuration makes maintenance difficult
  - Limited error handling and recovery
  - No input validation
  - Magic numbers for limits
  - No type safety for preferences
- Improvements needed:
  - Configuration management using dataclasses
  - Proper error hierarchy
  - Input validation
  - Type safety with TypedDict
  - Better logging and error recovery

---

## 3. **Jaccard Scores Module**

### ❌ Issues Found:

1. **Type Safety Issues**
```python
class Person:
    def __init__(self, user_id: int, preferences: Dict[str, str]):
        self.user_id = user_id
        self.preferences = preferences
        preferences['user_id'] = str(user_id) if user_id else "New User"
```

2. **Magic Numbers and Hardcoded Values**
```python
excluded_questions = ["FREE_TIME", "GENDER"]
```

3. **Complex String Manipulation**
```python
def get_unit_number(inventory_id: str) -> str:
    return inventory_id.split('-')[2]
```

### ✅ Recommended Improvements:

1. **Better Type Safety**
```python
from dataclasses import dataclass
from typing import Dict, List, Optional

@dataclass
class Preference:
    key: str
    value: str

@dataclass
class Person:
    user_id: Optional[int]
    preferences: Dict[str, str]
    
    def __post_init__(self):
        if self.user_id is not None:
            self.preferences['user_id'] = str(self.user_id)
        else:
            self.preferences['user_id'] = "New User"
```

2. **Configuration Management**
```python
class JaccardConfig:
    EXCLUDED_QUESTIONS = {"FREE_TIME", "GENDER"}
    NO_PREFERENCE_KEY = "_NO_PREFERENCE"
    MIN_OCCUPANTS = 2
```

3. **Better Unit Number Extraction**
```python
from dataclasses import dataclass
import re

@dataclass
class InventoryId:
    prefix: str
    building: str
    unit_number: str
    floor: str
    room: str

    @classmethod
    def from_string(cls, inventory_id: str) -> 'InventoryId':
        pattern = r'([A-Z]+)-([A-Z])-(\d+)-([A-Z]\d+)-([A-Z])'
        match = re.match(pattern, inventory_id)
        if not match:
            raise ValueError(f"Invalid inventory ID format: {inventory_id}")
        return cls(*match.groups())
```

4. **Improved Error Handling**
```python
class JaccardError(Exception):
    """Base exception for Jaccard score calculation errors."""
    pass

class InvalidPreferenceError(JaccardError):
    """Raised when preferences are invalid."""
    pass

class InsufficientOccupantsError(JaccardError):
    """Raised when there aren't enough occupants for comparison."""
    pass

def calculate_person_happiness(person: Person, unit_occupants: List[Person]) -> float:
    if len(unit_occupants) < JaccardConfig.MIN_OCCUPANTS:
        raise InsufficientOccupantsError(
            f"Need at least {JaccardConfig.MIN_OCCUPANTS} occupants for comparison"
        )
```

### 📝 Explanation:
- Current implementation issues:
  - Weak type safety in data classes
  - Hardcoded configuration values
  - Fragile string manipulation
  - Limited error handling
  - No input validation
- Improvements needed:
  - Proper data classes with type safety
  - Configuration management
  - Robust string parsing
  - Comprehensive error handling
  - Input validation
  - Better logging structure

---

## 4. **Pull Inventory Status Module**

### ❌ Issues Found:

1. **Configuration Management Issues**
```python
load_dotenv()
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASSWORD = os.getenv("DB_PASSWORD")
DB_HOST = os.getenv("DB_HOST")
DB_PORT = os.getenv("DB_PORT")
```

2. **Error Handling Gaps**
```python
except psycopg2.Error as e:
    if params:
        logger.error(f"Parameters were: {params}")  # Log the parameters
    return None
except Exception as e:
    logger.error(f"Error: {e}")  # Use logger.error instead of print
    return None
```

3. **Type Safety Issues**
```python
def get_inventory_status(required_category_type, move_in_date, society_id):
    # No type hints or validation
    if isinstance(required_category_type, str):
        categories = [required_category_type]
    else:
        categories = required_category_type
```

### ✅ Recommended Improvements:

1. **Configuration Management**
```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class DatabaseConfig:
    name: str
    user: str
    password: str
    host: str
    port: str

    @classmethod
    def from_env(cls) -> 'DatabaseConfig':
        load_dotenv()
        return cls(
            name=os.getenv("DB_NAME"),
            user=os.getenv("DB_USER"),
            password=os.getenv("DB_PASSWORD"),
            host=os.getenv("DB_HOST"),
            port=os.getenv("DB_PORT")
        )
```

2. **Better Error Handling**
```python
class DatabaseError(Exception):
    """Base exception for database operations."""
    pass

class QueryExecutionError(DatabaseError):
    """Raised when a query execution fails."""
    pass

class ConnectionError(DatabaseError):
    """Raised when database connection fails."""
    pass

def execute_query(connection_params: DatabaseConfig, query_file_path: str, params: Optional[dict] = None) -> List[dict]:
    try:
        query = read_sql_query(query_file_path)
        with psycopg2.connect(**connection_params.__dict__, cursor_factory=RealDictCursor) as conn:
            logger.info("Database connection established.")
            with conn.cursor() as cur:
                logger.info("Executing query...")
                if params:
                    logger.debug("Mogrified query:")
                    logger.debug(cur.mogrify(query, params).decode('utf-8'))
                    cur.execute(query, params)
                else:
                    cur.execute(query)
                    
                results = cur.fetchall()
                logger.info(f"Query executed successfully. {len(results)} rows returned.")
                return [dict(row) for row in results]

    except psycopg2.Error as e:
        logger.error(f"Database error: {e}")
        if params:
            logger.error(f"Parameters were: {params}")
        raise QueryExecutionError(f"Failed to execute query: {e}")
    except Exception as e:
        logger.error(f"Unexpected error: {e}")
        raise DatabaseError(f"Unexpected database error: {e}")
```

3. **Type Safety and Validation**
```python
from typing import List, Union
from datetime import date

def get_inventory_status(
    required_category_type: Union[str, List[str]],
    move_in_date: date,
    society_id: Union[str, List[str]]
) -> List[dict]:
    connection_params = DatabaseConfig.from_env()
    
    script_dir = os.path.dirname(os.path.abspath(__file__))
    sql_file_path = os.path.join(script_dir, 'nestquest_inventory_query.sql')
    
    categories = [required_category_type] if isinstance(required_category_type, str) else required_category_type
    societies = [society_id] if isinstance(society_id, str) else society_id
    
    params = {
        'categories': categories,
        'move_in_date': move_in_date,
        'societies': societies
    }
    
    try:
        results = execute_query(connection_params, sql_file_path, params)
        if results:
            logger.info(f"Query executed successfully. {len(results)} rows returned.")
            return results
        else:
            logger.warning("No results returned from the query.")
            return []
    except DatabaseError as e:
        logger.error(f"Failed to get inventory status: {e}")
        return []
```

### 📝 Explanation:
- Current implementation issues:
  - Global configuration variables
  - Inconsistent error handling
  - Lack of type hints
  - No input validation
  - Limited error recovery
- Improvements needed:
  - Configuration management using dataclasses
  - Proper exception hierarchy
  - Type hints and validation
  - Consistent error handling
  - Better logging structure
  - Input validation

---

## 5. **SQL Query Module**

### ❌ Issues Found:

1. **Query Structure Issues**
```sql
with available_units as (
    select distinct u.id as unitId
    from inventories
    left join rooms on inventories.parent_id = rooms.id
    left join units u on rooms.unit_id = u.id
    left join tenant_booking_status tbs on tbs.inventory_id = inventories.id
    where inventories.is_active = true
    and inventories.society_id = any(%(societies)s)
    and inventories.status = 'AVAILABLE'
    and inventories.item_type = 'BED'
    and ("inventories"."status" = 'AVAILABLE' or 
    (tbs."checkout_date" <  %(move_in_date)s and tbs."status" in ('MOVE_IN_SUCCESS', 'MOVE_IN_PENDING', 'SERVICE_AGREEMENT_PENDING')))
    and inventories.category = ANY(%(categories)s)
    and inventories.gender = ANY(%(genders)s)
)
```

2. **Parameter Validation Issues**
```sql
where
    u.id in (select unitId from available_units)
    and (i.status <> 'AVAILABLE' or (i.category = ANY(%(categories)s) and i.status = 'AVAILABLE'))
    and i.item_type = 'BED'
    and (up.is_active = true or up.is_active is null)
    and (up.is_active = true or up.is_active is null)
    and (up.is_complete = true or up.is_complete is null)
```

3. **Query Performance Issues**
```sql
left join configuration c on c.key = 'INVENTORY_IMAGES_OCCUPANCY_WISE'
left join rooms r on i.parent_id = r.id
left join units u on r.unit_id = u.id
left join tenant_booking_status tbs on tbs.inventory_id = i.id
left join users on tbs.user_id = users.id
left join user_preference up on up.user_id = users.id
```

### ✅ Recommended Improvements:

1. **Query Structure and Readability**
```sql
WITH available_units AS (
    SELECT DISTINCT 
        u.id AS unit_id
    FROM inventories i
    LEFT JOIN rooms r ON i.parent_id = r.id
    LEFT JOIN units u ON r.unit_id = u.id
    LEFT JOIN tenant_booking_status tbs ON tbs.inventory_id = i.id
    WHERE i.is_active = TRUE
        AND i.society_id = ANY(%(societies)s)
        AND i.status = 'AVAILABLE'
        AND i.item_type = 'BED'
        AND (
            i.status = 'AVAILABLE' 
            OR (
                tbs.checkout_date < %(move_in_date)s 
                AND tbs.status IN (
                    'MOVE_IN_SUCCESS', 
                    'MOVE_IN_PENDING', 
                    'SERVICE_AGREEMENT_PENDING'
                )
            )
        )
        AND i.category = ANY(%(categories)s)
        AND i.gender = ANY(%(genders)s)
)
```

2. **Parameter Validation and Type Safety**
```sql
-- Add parameter validation comments
-- @param societies: Array of society IDs
-- @param move_in_date: Date in YYYY-MM-DD format
-- @param categories: Array of category types
-- @param genders: Array of gender values

SELECT
    i.id AS booking_id,
    u.id AS unit_id,
    i.status,
    i.id,
    i.inventory_id,
    i.category,
    jsonb_extract_path_text(c.value::jsonb, i.category) AS image_url,
    up.user_id,
    up.preferences
FROM inventories i
```

3. **Query Performance Optimization**
```sql
-- Add indexes for frequently joined columns
-- CREATE INDEX idx_inventories_parent_id ON inventories(parent_id);
-- CREATE INDEX idx_rooms_unit_id ON rooms(unit_id);
-- CREATE INDEX idx_tenant_booking_status_inventory_id ON tenant_booking_status(inventory_id);
-- CREATE INDEX idx_user_preference_user_id ON user_preference(user_id);

-- Use INNER JOIN where appropriate instead of LEFT JOIN
INNER JOIN configuration c ON c.key = 'INVENTORY_IMAGES_OCCUPANCY_WISE'
INNER JOIN rooms r ON i.parent_id = r.id
INNER JOIN units u ON r.unit_id = u.id
LEFT JOIN tenant_booking_status tbs ON tbs.inventory_id = i.id
LEFT JOIN users ON tbs.user_id = users.id
LEFT JOIN user_preference up ON up.user_id = users.id
```

### 📝 Explanation:
- Current implementation issues:
  - Complex nested conditions
  - Redundant joins
  - Lack of parameter validation
  - Inconsistent table aliases
  - Missing performance optimizations
- Improvements needed:
  - Better query structure and formatting
  - Parameter validation and documentation
  - Performance optimization with indexes
  - Consistent table aliases
  - Proper join types
  - Query comments and documentation

---

## 6. **Next Steps**

Let me examine the next module for review. 