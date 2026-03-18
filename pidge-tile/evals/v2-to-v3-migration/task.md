# Upgrade Legacy Notification Code to Current Library Version

## Problem/Feature Description

A logistics company's shipping notification service was built two years ago and has been running reliably in production. However, the pidge notification library recently released a major new version with breaking API changes, and the old version is no longer receiving security patches. The team needs to upgrade urgently.

The existing codebase uses the older pidge API. The new version has a completely different client interface and must be used going forward. Your task is to rewrite the notification module so it works with the current pidge library (`pip install pidge`) while preserving the same overall behaviour: sending shipping update emails to customers.

## Output Specification

Produce a file `shipping_notify.py` with a fully updated implementation that:

- Sends shipping status notifications (e.g. "Your package has shipped", with a tracking number)
- Works with the currently available pidge library
- Handles errors gracefully and logs relevant information
- Returns an indication of whether the notification was successfully delivered

## Input Files

The following file shows the current (legacy) implementation. Extract it before beginning.

=============== FILE: inputs/shipping_notify_legacy.py ===============
import os
import logging
from pidge import Notifier, NotifierConfig

logger = logging.getLogger(__name__)

PIDGE_KEY = "sk-prod-a1b2c3d4e5f6"  # production key

def send_shipping_update(customer_email: str, tracking_number: str, carrier: str) -> bool:
    """Send a shipping update notification to a customer."""
    cfg = NotifierConfig(key=PIDGE_KEY)
    notifier = Notifier(cfg)

    msg = notifier.create_message(
        customer_email,
        "Your package has shipped!",
        f"Great news! Your order is on its way.\nTracking: {tracking_number} via {carrier}.",
    )

    try:
        result = notifier.send(msg)
        if result.ok:
            logger.info("Shipping notification sent to %s", customer_email)
            return True
        else:
            logger.warning("Notification not confirmed for %s", customer_email)
            return False
    except Exception as e:
        logger.error("Failed to send notification: %s", e)
        return False
