# Code Review: Non-Standard Practices and Fixes

This document highlights **bad coding practices** found in the current version of the lead scoring and summarization pipeline, along with recommended **fixes or refactors**. Use this as a guide for training and improving engineering standards.

---

## 1. **Hardcoded Retry Mechanism with Random Delay**

### ❌ Bad Practice:
```python
attempt = 0
max_retries = 10
while attempt < max_retries:
    ...
    sleep_time = random.randint(30,60)
    time.sleep(sleep_time)
```

### ✅ Recommended Fix:
Use **exponential backoff** with capped sleep time.
```python
import math
sleep_time = min(2 ** attempt, 60)
time.sleep(sleep_time)
```

---

## 2. **Excessive Use of Print Statements in Production**

### ❌ Bad Practice:
```python
print("++response_text body++"*3)
print(response_text)
```

### ✅ Recommended Fix:
Use the logging module.
```python
import logging
logger = logging.getLogger(__name__)
logger.info("Claude response: %s", response_text)
```

---

## 3. **Overuse of Comments and Dead Code**

### ❌ Bad Practice:
```python
# def lead_summary_1(...):
#     try:
#         # Format the system prompt
#         formatted_analysis_prompt = ...
```

### ✅ Recommended Fix:
Remove commented-out functions and use version control to keep old code.
Avoid verbose comments that repeat what code does.
Keep comments **brief**, **high-value**, and limited to explaining intent, not mechanics.

---

## 4. **Inconsistent and Vague Naming Conventions**

### ❌ Bad Practice:
```python
client = boto3.client(...)
client_1 = boto3.client(...)
REASON_1 = 'Agent has placed a call...'
REASON_21 = '...'
```

### ✅ Recommended Fix:
Use short but meaningful names that reflect purpose:
```python
main_client = boto3.client(...)
haiku_client = boto3.client(...)
REASON_INITIAL = '...'
REASON_FOLLOWUP = '...'
```
Avoid suffixes like `_1`, `_2`. Instead, **encode the context** in the name (e.g. `haiku`, `main`, `whisper`). Constants should follow a consistent naming convention using concise identifiers.

---

## 5. **Using `str.split()` Without Safeguards**

### ❌ Bad Practice:
```python
trans_len = len(transcript.split())
```

### ✅ Recommended Fix:
Ensure type-safety before calling string methods.
```python
trans_len = len(str(transcript).split())
```

---

## 6. **Global Prompt Loading Without Caching or Lazy Loading**

### ❌ Bad Practice:
```python
with open(site_root + "/prompts.json", "r") as json_file:
    prompt = json.load(json_file)
```

### ✅ Recommended Fix:
Wrap in a reusable lazy loader function.
```python
def load_prompts(path="prompts.json"):
    with open(path, "r") as f:
        return json.load(f)
```

---

## 7. **No Type Hinting for Function Returns**

### ❌ Bad Practice:
```python
def generate_transcript(aud):
```

### ✅ Recommended Fix:
```python
def generate_transcript(aud: str) -> Optional[str]:
```

---

## 8. **Handling JSON Extraction via Regex**

### ❌ Bad Practice:
```python
match = re.search(r'\{.*\}', response_text, re.DOTALL)
```

### ✅ Recommended Fix:
Validate actual JSON boundaries and wrap in a try-catch block with fallback:
```python
try:
    return json.loads(response_text)
except json.JSONDecodeError:
    # handle fallback
```

---

## 9. **No Retry or Timeout Logic on External Requests**

### ❌ Bad Practice:
```python
response = requests.post(AUDIO_MODEL, json=data)
```

### ✅ Recommended Fix:
```python
response = requests.post(AUDIO_MODEL, json=data, timeout=20)
```
Or use a `Session` with retry logic from `requests.adapters`.

---

## 10. **String Concatenation Using `+` Instead of f-strings**

### ❌ Bad Practice:
```python
text = text + "Call (" + i + ") Transcript :" + modified_transcript + "\n"
```

### ✅ Recommended Fix:
```python
text += f"Call ({i}) Transcript : {modified_transcript}\n"
```

---

## 11. **Mutating External Payload Object**

### ❌ Bad Practice:
```python
payload["chatsData"] = ""
```

### ✅ Recommended Fix:
Copy the payload first to avoid side-effects.
```python
payload = payload.copy()
```

---

## 12. **Repetitive Code Blocks for Similar Logic**

### ❌ Bad Practice:
```python
P = 0 if not isinstance(P, (int, float)) or P is None else P
A = 0 if not isinstance(A, (int, float)) or A is None else A
...
```

### ✅ Recommended Fix:
```python
P, A, I, R = [max(0, x if isinstance(x, (int, float)) else 0) for x in (P, A, I, R)]
```

---

## 13. **No Unit Tests for Helper Functions**

### ❌ Bad Practice:
Many of the helper methods (`extract_summary`, `extract_first_score`, etc.) are complex and untested.

### ✅ Recommended Fix:
Place these in `utils/` or `helpers/` folder with `test_*.py` files under `tests/`.

---

## 14. **No Retry Logic for Claude API Call**

### ❌ Bad Practice:
```python
response = client_1.invoke_model(...)
```

### ✅ Recommended Fix:
Wrap this with retry using `backoff` or a simple loop with exponential retry logic.

---

## 15. **Multiple Responsibility in One Function**

### ❌ Bad Practice:
`get_scores()` is extremely large and does:
- Transcript extraction
- Chat/Manual analysis
- Summary generation
- PAIR scoring
- All stage adjustments

### ✅ Recommended Fix:
Break into:
- `get_transcripts()`
- `refine_inputs()`
- `generate_summary()`
- `adjust_scores()`

---

## 16. **Mixing Concerns Across Layers**

This code mixes **business logic**, **API logic**, and **data processing** in one layer. This makes it difficult to:
- Unit test
- Scale
- Debug

### ✅ Fix: Apply Clean Architecture Principles
- **Service Layer**: `bedrock_service.py`
- **Business Logic**: `lead_score_service.py`
- **Data Models**: `schemas.py`

---

## Conclusion
While the current pipeline works, these improvements will:
- Reduce bugs
- Improve testability
- Make the system maintainable and production-grade

> Use this document to build linting rules, code review checklists, and onboarding guides.

---
# Code Review: Improvements to Task Guru Score Generation

This document highlights the **improvements** made to the task guru score generation pipeline, along with explanations of why certain practices were problematic and how they were fixed.

---

## 1. **Inconsistent Logging Practices**

### ❌ Original Code:
```python
print("++response_text body++"*3)
print(response_text)
print("TRANCRIPT", transcript)
print("="*30)
```

### ✅ Improved Code:
```python
logger.debug("Transcript Response Status: %d", response.status_code)
logger.info("Successfully generated transcript")
logger.warning("Invalid response format from audio model")
logger.error("Error in generate_transcript: %s", str(e))
```

### 📝 Explanation:
- Print statements are not suitable for production code as they:
  - Cannot be easily filtered or redirected
  - Don't provide timestamps or log levels
  - Cannot be configured for different environments
  - Make debugging and monitoring difficult
- Proper logging provides:
  - Different severity levels (debug, info, warning, error)
  - Structured output with timestamps
  - Configurable output destinations
  - Better production monitoring capabilities

---

## 2. **Poor Error Handling**

### ❌ Original Code:
```python
try:
    response = requests.post(AUDIO_MODEL, json=data)
except Exception as e:
    print(f"Error: {str(e)}")
```

### ✅ Improved Code:
```python
try:
    response = requests.post(AUDIO_MODEL, json=data, timeout=30)
    if response.status_code == 200:
        # Process response
    elif response.status_code == 500:
        # Handle retry with exponential backoff
    else:
        logger.error(f"Unexpected status code: {response.status_code}")
except requests.exceptions.RequestException as e:
    logger.error(f"Request error: {str(e)}")
    if attempt < max_retries - 1:
        attempt += 1
        time.sleep(sleep_time)
    else:
        raise SummaryResponseError("generate_transcript", str(e))
```

### 📝 Explanation:
- Original code had:
  - Generic exception handling
  - No retry mechanism
  - No timeout handling
  - No proper error propagation
- Improved code includes:
  - Specific exception handling
  - Exponential backoff retry logic
  - Request timeouts
  - Proper error logging and propagation
  - Custom exception types for better error handling

---

## 3. **Inconsistent Variable Naming**

### ❌ Original Code:
```python
client = boto3.client(...)
client_1 = boto3.client(...)
REASON_1 = 'First interaction...'
REASON_21 = 'Follow-up...'
```

### ✅ Improved Code:
```python
bedrock_client = boto3.client(...)
haiku_client = boto3.client(...)
FIRST_INTERACTION_REASON = 'First interaction...'
FOLLOW_UP_SAME_STAGE_REASON = 'Follow-up...'
```

### 📝 Explanation:
- Original naming was:
  - Inconsistent (mixing camelCase and UPPER_CASE)
  - Unclear (using numeric suffixes)
  - Not descriptive of purpose
- Improved naming:
  - Uses consistent UPPER_CASE for constants
  - Descriptive names that indicate purpose
  - Follows Python naming conventions
  - Makes code more maintainable and readable

---

## 4. **Lack of Type Hints and Documentation**

### ❌ Original Code:
```python
def generate_transcript(aud):
    # Function implementation
```

### ✅ Improved Code:
```python
def generate_transcript(audio_url: str) -> Optional[str]:
    """
    Generate transcript from audio URL using the audio model.
    
    Args:
        audio_url (str): URL of the audio file to transcribe
        
    Returns:
        Optional[str]: Generated transcript or None if transcription fails
        
    Raises:
        SummaryResponseError: If transcription fails after max retries
    """
    # Function implementation
```

### 📝 Explanation:
- Original code lacked:
  - Type hints for parameters and return values
  - Documentation of function purpose
  - Documentation of parameters and return values
  - Documentation of exceptions
- Improved code includes:
  - Type hints for better IDE support
  - Comprehensive docstrings
  - Clear parameter and return value documentation
  - Exception documentation
  - Better code maintainability

---

## 5. **Hardcoded Values and Magic Numbers**

### ❌ Original Code:
```python
sleep_time = random.randint(30,60)
max_retries = 10
```

### ✅ Improved Code:
```python
MAX_RETRIES = 10
BASE_SLEEP_TIME = 30
MAX_SLEEP_TIME = 300  # 5 minutes

sleep_time = min(BASE_SLEEP_TIME * (2 ** attempt), MAX_SLEEP_TIME)
```

### 📝 Explanation:
- Original code had:
  - Hardcoded values without explanation
  - Magic numbers in calculations
  - No clear constants for configuration
- Improved code:
  - Uses named constants
  - Implements exponential backoff
  - Makes configuration values clear and maintainable
  - Follows DRY (Don't Repeat Yourself) principle

---

## 6. **Poor Input Validation**

### ❌ Original Code:
```python
trans_len = len(transcript.split())
```

### ✅ Improved Code:
```python
if not isinstance(transcript, str):
    logger.warning("Non-string input provided to clean_transcript")
    return str(transcript)

trans_len = len(str(transcript).split())
```

### 📝 Explanation:
- Original code:
  - Assumed input type without validation
  - Could fail with non-string inputs
  - No error handling for edge cases
- Improved code:
  - Validates input types
  - Handles edge cases gracefully
  - Provides appropriate logging
  - Returns safe default values

---

## 7. **Inconsistent Return Types**

### ❌ Original Code:
```python
def extract_score(text):
    # Could return None, int, or raise exception
```

### ✅ Improved Code:
```python
def extract_score(text: str) -> Optional[int]:
    """
    Extract sentiment score from text.
    
    Args:
        text (str): Text containing the sentiment score to extract
        
    Returns:
        Optional[int]: Extracted sentiment score or None if not found
        
    Raises:
        SummaryResponseError: If score extraction fails
    """
    # Function implementation
```

### 📝 Explanation:
- Original code:
  - Had inconsistent return types
  - No clear error handling strategy
  - No type hints
  - Unclear function contract
- Improved code:
  - Clear return type annotation
  - Consistent error handling
  - Proper type hints
  - Clear function contract
  - Better maintainability

---

## 8. **Poor Code Organization**

### ❌ Original Code:
```python
# All functions in one file
# No clear separation of concerns
# Mixed business logic and API calls
```

### ✅ Improved Code:
```python
# Separated into logical components:
# - API clients
# - Data processing
# - Business logic
# - Error handling
# - Logging configuration
```

### 📝 Explanation:
- Original code:
  - Mixed concerns
  - Difficult to test
  - Hard to maintain
  - No clear separation of responsibilities
- Improved code:
  - Clear separation of concerns
  - Better testability
  - Easier maintenance
  - Clear responsibility boundaries
  - Better code organization

---

## Conclusion

The improvements made to the codebase have significantly enhanced its:
- Maintainability
- Reliability
- Testability
- Production-readiness
- Error handling
- Logging capabilities
- Code organization
- Documentation

These changes follow Python best practices and make the code more robust and maintainable for production use.

> Use this document as a reference for future code reviews and improvements. 
