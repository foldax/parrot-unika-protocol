# parrot-unika-protocol

Documentation for the Parrot UNIKA protocol, including message structure,
example traffic, and implementation snippets.

## Protocol overview

UNIKA is a lightweight request/response protocol with:

1. **Handshake**: device and server exchange identity and protocol version.
2. **Telemetry**: device sends periodic status updates.
3. **Command**: server sends command messages and expects acknowledgements.
4. **Keepalive**: both sides send heartbeats to keep the session active.

## Message model

Messages are UTF-8 JSON objects.

Required fields:

- `type`: message kind (`hello`, `telemetry`, `command`, `ack`, `heartbeat`)
- `seq`: monotonically increasing sequence number
- `ts`: RFC3339 UTC timestamp
- `payload`: type-specific data

Example envelope:

```json
{
  "type": "telemetry",
  "seq": 42,
  "ts": "2026-04-16T21:19:28Z",
  "payload": {
    "device_id": "parrot-01",
    "battery_pct": 91,
    "temperature_c": 24.5
  }
}
```

## Session example

```text
Client -> Server: hello(seq=1, version=1.0, device_id=parrot-01)
Server -> Client: ack(seq=1, status=ok)
Client -> Server: telemetry(seq=2, battery_pct=91, temperature_c=24.5)
Server -> Client: command(seq=3, action=calibrate, axis=roll)
Client -> Server: ack(seq=3, status=ok)
Client -> Server: heartbeat(seq=4)
Server -> Client: heartbeat(seq=5)
```

## Sample logs

```log
2026-04-16T21:19:29Z INFO  [unika.client] send type=hello seq=1 device_id=parrot-01 version=1.0
2026-04-16T21:19:29Z INFO  [unika.server] recv type=hello seq=1 device_id=parrot-01
2026-04-16T21:19:29Z INFO  [unika.server] send type=ack seq=1 status=ok
2026-04-16T21:19:35Z DEBUG [unika.client] send type=telemetry seq=2 battery_pct=91 temperature_c=24.5
2026-04-16T21:19:36Z INFO  [unika.server] send type=command seq=3 action=calibrate axis=roll
2026-04-16T21:19:36Z INFO  [unika.client] recv type=command seq=3 action=calibrate axis=roll
2026-04-16T21:19:36Z INFO  [unika.client] send type=ack seq=3 status=ok
```

## Code snippets

### Python: build and serialize a telemetry message

```python
import json
from datetime import datetime, timezone

msg = {
    "type": "telemetry",
    "seq": 42,
    "ts": datetime.now(timezone.utc).isoformat().replace("+00:00", "Z"),
    "payload": {
        "device_id": "parrot-01",
        "battery_pct": 91,
        "temperature_c": 24.5,
    },
}

wire = json.dumps(msg, separators=(",", ":"))
print(wire)
```

### JavaScript: parse and route incoming messages

```js
function handleMessage(raw) {
  const msg = JSON.parse(raw);
  switch (msg.type) {
    case "command":
      return executeCommand(msg.payload);
    case "heartbeat":
      return { type: "heartbeat", seq: msg.seq + 1, ts: new Date().toISOString(), payload: {} };
    default:
      return { type: "ack", seq: msg.seq, ts: new Date().toISOString(), payload: { status: "ok" } };
  }
}
```
