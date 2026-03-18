# Resilient Password Reset Notification Service

## Problem/Feature Description

A SaaS platform's password reset flow uses the pidge notification library to deliver reset-link emails. The infrastructure team has reported that during peak traffic periods, some notifications fail due to momentary network hiccups between the application and the pidge API — these are short-lived and typically succeed on a second or third attempt. However, other failures (like emails sent to deactivated or malformed addresses) are permanent and wasting retries only delays the response to the user.

The team needs a robust `send_password_reset` async function that wraps the pidge dispatch call with smart retry behaviour: retrying on transient failures while giving up immediately on permanent ones. The function should be production-ready — logging what happened at each stage and making the outcome clear to the caller.

The pidge library (`pip install pidge`) is already available. The `PIDGE_API_KEY` environment variable will be set at runtime.

## Output Specification

Produce a Python file `password_reset_notify.py` containing:

1. An async function `send_password_reset(email: str, reset_url: str) -> dict` that:
   - Dispatches a password reset notification to the given email address
   - Implements retry logic for transient failures
   - Returns a dict with outcome information for the caller

2. A brief `if __name__ == "__main__":` block that demonstrates calling the function with a sample email and URL (using `asyncio.run`).

Include all imports and make the file self-contained.
