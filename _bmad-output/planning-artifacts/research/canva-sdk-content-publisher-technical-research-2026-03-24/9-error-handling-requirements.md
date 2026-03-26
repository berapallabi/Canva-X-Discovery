# 9. Error Handling Requirements

## In `publishContent`:
- Return `AppError` for unrecoverable errors (generic error message shown above Publish button)
- Return custom error message for recoverable errors (e.g., "Your X session has expired — reconnect your account")
- Provide custom Settings UI treatment for field-level errors

## Auth errors (minimum required):
- Server error message
- Catch-all fallback message
- Permission denied message
- Temporarily unavailable message

---
