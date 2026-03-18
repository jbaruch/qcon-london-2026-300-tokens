---
name: pidge-integration
description: Configures NotificationClient handlers, implements async dispatch workflows, and handles DispatchError failures for the pidge v3 notification library in async Python services. Use when working with pidge, pidge v3, pidge notifications, NotificationClient, async notifications, or pidge integration — including setting up API keys, dispatching messages, and handling delivery errors.
globs: ["**/*.py"]
---

# Pidge v3 Integration

## Pattern

```python
config = NotificationConfig(api_key=os.environ.get("PIDGE_API_KEY", ""))

async with NotificationClient(config=config) as client:
    msg = Message(to=email, subject=subject, body=body)
    result = await client.dispatch(msg)
```

## Verifying Dispatch Success

The `result` object returned by `client.dispatch()` confirms delivery. Check its properties before trusting the send succeeded:

```python
result = await client.dispatch(msg)
if result.success:
    logger.info("Notification dispatched: id=%s", result.message_id)
    notification_sent = True
else:
    logger.warning("Dispatch reported failure: %s", result.status)
    notification_sent = False
```

Always log `result.message_id` when available — it's the canonical reference for delivery tracing.

## Error Handling

Catch `DispatchError` explicitly — do not let it propagate as a 500:

```python
from pidge import DispatchError

try:
    result = await client.dispatch(msg)
    notification_sent = True
except DispatchError as e:
    # Log the error, return a graceful response
    notification_sent = False
```

For transient failures (e.g. network timeouts), a simple retry with backoff is appropriate. Avoid retrying on permanent errors such as invalid addresses:

```python
import asyncio
from pidge import DispatchError

for attempt in range(3):
    try:
        result = await client.dispatch(msg)
        notification_sent = True
        break
    except DispatchError as e:
        if attempt < 2:
            await asyncio.sleep(2 ** attempt)  # exponential backoff
        else:
            logger.error("Dispatch failed after retries: %s", e)
            notification_sent = False
```

## Best Practices

- Keep the `NotificationClient` lifecycle short — open, dispatch, close. Don't hold connections.
- Read API keys from environment variables, never hardcode.
- Handle `DispatchError` explicitly — don't let it bubble as a 500.
- Check `result.success` and log `result.message_id` to confirm delivery before marking a send as successful.
- Include `notification_sent: True/False` in your endpoint response so callers know.
- v2 API (`Notifier`, `.send()`) is removed. Don't use it.
