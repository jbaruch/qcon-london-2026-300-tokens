# Pidge v3 API Reference

## Overview

Pidge is a notification delivery library for sending transactional messages (order confirmations, password resets, shipping updates). v3 uses an async context manager pattern.

## Installation

```bash
pip install pidge
```

## Classes

### NotificationClient

Async notification client. Must be used as an async context manager.

```python
from pidge import NotificationClient, NotificationConfig

config = NotificationConfig(
    api_key="your-api-key",
    endpoint="https://api.pidge.dev/v3",
)
async with NotificationClient(config=config) as client:
    result = await client.dispatch(msg)
```

#### Methods

- `dispatch(message: Message) -> DispatchResult` - Send a notification. Must be called within the async context manager.

### Message

Dataclass for notification content.

```python
from pidge import Message

msg = Message(
    to="customer@example.com",
    subject="Order Confirmed",
    body="Your order #ORD-1234 has been confirmed.",
    priority="normal",     # optional, default "normal"
    metadata={"order_id": "ord_abc123"},  # optional
)
```

### NotificationConfig

```python
from pidge import NotificationConfig

config = NotificationConfig(
    api_key="your-api-key",                # required
    endpoint="https://api.pidge.dev/v3",   # default
    timeout=30,                            # default
    retry_count=3,                         # default
)
```

### DispatchResult

Returned by `client.dispatch()`.

- `status: Status` - Status.SENT, Status.FAILED, or Status.PENDING
- `message_id: str` - Unique message identifier
- `details: Optional[str]` - Additional status info

### Status (Enum)

- `Status.SENT`
- `Status.FAILED`
- `Status.PENDING`

## Integration Pattern

```python
from pidge import NotificationClient, NotificationConfig, Message, Status

config = NotificationConfig(api_key=os.environ["PIDGE_API_KEY"])

async with NotificationClient(config=config) as client:
    msg = Message(
        to=order.customer_email,
        subject=f"Order {order.order_id} Confirmed",
        body=f"Your order {order.order_id} has been confirmed.",
    )
    result = await client.dispatch(msg)
    notification_sent = result.status == Status.SENT
```

## Error Handling

The client raises `RuntimeError` if used outside an async context manager.
Network and API errors raise `DispatchError`:

```python
from pidge import DispatchError

try:
    result = await client.dispatch(msg)
except DispatchError as e:
    logger.error("dispatch_failed", code=e.code, message=e.message)
```
