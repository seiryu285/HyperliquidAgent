# Order Executor 2025 Pseudo-Code

This document demonstrates the high level flow for an Order Executor compatible with the Hyperliquid 2025 API.

```python
# Establish persistent websocket connections
ws_limit = connect_ws("/mm/limit")
ws_cancel = connect_ws("/mm/cancel")

BATCH_INTERVAL_MS = 50

while True:
    start = current_time_ms()
    
    # 1. Retrieve quote updates from the Quote Engine
    new_orders, cancel_orders = quote_engine.collect()
    
    # 2. Sign and submit new limit orders in batches
    for order in new_orders:
        signed = sign(order)
        ws_limit.send(signed)
    
    # 3. Send cancellations if needed
    for cancel in cancel_orders:
        ws_cancel.send(cancel)
    
    # 4. Wait for acknowledgements from the exchange
    await_ack(ws_limit, ws_cancel)
    
    # 5. Sleep until next batch interval
    elapsed = current_time_ms() - start
    sleep(max(0, BATCH_INTERVAL_MS - elapsed))
```

The pseudo-code assumes that signing keys and API credentials are securely stored, as specified in the system specification. The actual implementation must also handle error recovery and latency monitoring.
