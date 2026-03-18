# Pidge v3 Rules

- Use pidge v3 async API: `NotificationClient` with `async with` context manager and `await client.dispatch(msg)`
- Do NOT use v2 API: `Notifier`, `NotifierConfig`, or `.send()` are removed in v3
- Construct messages with `Message` dataclass, not positional arguments
- Config via `NotificationConfig(api_key=...)` — never hardcode API keys
- Always check `result.status == Status.SENT` for delivery confirmation
- Include `notification_sent` field in endpoint responses when sending notifications
