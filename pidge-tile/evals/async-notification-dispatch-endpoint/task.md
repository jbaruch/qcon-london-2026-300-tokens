# Order Confirmation Notification Endpoint

## Problem/Feature Description

An e-commerce platform is building a post-checkout flow. When a customer completes a purchase, a backend service needs to send them an order confirmation notification via the pidge notification library (v3). The team has a FastAPI service already running order processing logic, and they need a new `/notify/order-confirmed` endpoint added to it.

The endpoint receives the order details and customer email, then dispatches a notification. Since this endpoint is hit in the critical checkout path, it must be resilient: delivery failures should not crash the endpoint or surface as server errors to the caller. The caller (frontend checkout service) may take follow-up actions based on the result, so the response should be informative.

The pidge library is available via `pip install pidge`. The team's API key will be available at runtime via an environment variable.

## Output Specification

Produce a Python file `order_notify.py` containing a FastAPI app (or router) with the `/notify/order-confirmed` POST endpoint implemented. The endpoint should:

- Accept a JSON body with at least `customer_email`, `order_id`, and `order_total` fields
- Send an order confirmation notification to the customer
- Return a JSON response with appropriate status information for the caller
- Handle notification failures gracefully without returning a 5xx error

Include any necessary imports. The file should be runnable (e.g., `uvicorn order_notify:app`).
