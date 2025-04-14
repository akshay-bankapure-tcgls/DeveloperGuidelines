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

## 3. **Using `str.split()` Without Safeguards**

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

## 4. **Global Prompt Loading Without Caching or Lazy Loading**

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

## 5. **No Type Hinting for Function Returns**

### ❌ Bad Practice:
```python
def generate_transcript(aud):
```

### ✅ Recommended Fix:
```python
def generate_transcript(aud: str) -> Optional[str]:
```

---

## 6. **Handling JSON Extraction via Regex**

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

## 7. **No Retry or Timeout Logic on External Requests**

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

## 8. **String Concatenation Using `+` Instead of f-strings**

### ❌ Bad Practice:
```python
text = text + "Call (" + i + ") Transcript :" + modified_transcript + "\n"
```

### ✅ Recommended Fix:
```python
text += f"Call ({i}) Transcript : {modified_transcript}\n"
```

---

## 9. **Mutating External Payload Object**

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

## 10. **Repetitive Code Blocks for Similar Logic**

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

## 11. **No Unit Tests for Helper Functions**

### ❌ Bad Practice:
Many of the helper methods (`extract_summary`, `extract_first_score`, etc.) are complex and untested.

### ✅ Recommended Fix:
Place these in `utils/` or `helpers/` folder with `test_*.py` files under `tests/`.

---

## 12. **No Retry Logic for Claude API Call**

### ❌ Bad Practice:
```python
response = client_1.invoke_model(...)
```

### ✅ Recommended Fix:
Wrap this with retry using `backoff` or a simple loop with exponential retry logic.

---

## 13. **Multiple Responsibility in One Function**

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

## 14. **Mixing Concerns Across Layers**

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

