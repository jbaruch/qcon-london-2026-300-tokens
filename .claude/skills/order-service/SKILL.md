---
name: order-service
description: Defines coding conventions, error handling patterns, async practices, logging standards, API response formats, and testing workflows for the Order Service — a FastAPI/Python microservice. Covers typed exception classes with FastAPI exception handler registration, structlog structured logging, Pydantic request/response models, and pytest-asyncio endpoint tests. Use when writing or modifying order endpoints, implementing order processing logic, adding error handling, or working with any file matching app/**/*.py or tests/**/*.py in the Order Service.
alwaysApply: true
---

# Order Service - Development Skill

## Project Structure

```
app/
  main.py       - FastAPI app, exception handlers
  orders.py     - Order endpoints
  models.py     - Pydantic request/response models
tests/
  conftest.py   - Shared fixtures (client, sample_order)
  test_*.py     - Async endpoint tests
```

## Error Handling

Use typed exception classes. Never use bare try/except at the endpoint level.

Define exceptions per domain:

```python
class OrderNotFoundError(Exception):
    def __init__(self, order_id: str):
        self.order_id = order_id
        super().__init__(f"Order {order_id} not found")
```

Register exception handlers on the FastAPI app in `main.py`:

```python
from fastapi.responses import JSONResponse

@app.exception_handler(OrderNotFoundError)
async def handle_order_not_found(request, exc):
    return JSONResponse(
        status_code=404,
        content={
            "error": "OrderNotFound",
            "order_id": exc.order_id,
            "message": str(exc),
        },
    )
```

In endpoints, raise the exception and let the handler deal with it:

```python
if order_id not in _orders:
    raise OrderNotFoundError(order_id)
```

### Error Response Format

```json
{
    "error": "ErrorTypeName",
    "order_id": "the-resource-id",
    "message": "Human-readable description"
}
```

### HTTP Status Codes

- 404 for resources not found
- 409 for state conflicts (e.g., confirming an already-confirmed order)
- 422 for validation failures
- 200 for success

## Async Patterns

All endpoint handlers are async. All I/O operations use async/await.
Never use synchronous blocking calls inside an endpoint.

External service calls (database, third-party APIs) must be awaited.

## Logging

Use structlog for all logging. Never use print().

```python
import structlog
logger = structlog.get_logger()

logger.info("order_confirmed", order_id=order_id)
logger.error("order_not_found", order_id=order_id)
```

## Response Conventions

All successful mutation endpoints return the resource ID and new status:

```python
return {"order_id": order_id, "status": "confirmed"}
```

Include additional fields when the endpoint triggers side effects.

## Testing

- Use `pytest` with `pytest-asyncio` (asyncio_mode = auto)
- Use `httpx.AsyncClient` with `ASGITransport` for endpoint tests
- Use the `client` and `sample_order` fixtures from `conftest.py`
- Mock external services with `unittest.mock.AsyncMock`

### Test Workflow

1. **Obtain fixtures** — use the shared `client` and `sample_order` fixtures from `conftest.py`.
2. **Patch external dependencies** — replace external service calls with `AsyncMock` before invoking the endpoint.
3. **Call the endpoint** — issue the request via the `AsyncClient`.
4. **Assert status code** — verify the expected HTTP status code (200, 404, 409, etc.).
5. **Assert response body** — check all relevant fields in the JSON response.
6. **If failures occur** — verify mock setup first (correct patch target, `AsyncMock` vs `MagicMock`), then re-run until all assertions pass.

Run tests:

```bash
python -m pytest tests/ -v
```

## Memory Bank

Architectural decisions are stored in `.memory/decisions/`:

```
.memory/decisions/
  YYYY-MM-DD-short-description.md
```

Each file:

```markdown
# Decision: [short title]
Date: YYYY-MM-DD
Status: accepted

## Context
[Why this decision was needed]

## Decision
[What was decided]

## Consequences
[What this means for the codebase]
```

## Dependencies

- **FastAPI** - Web framework
- **structlog** - Structured logging
- **Pydantic** - Request/response validation
