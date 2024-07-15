# Client Heartbeat Message

The client sends this message periodically to the server after establishing a connection. Its purpose is to:

1. Inform the server that the client is still active and connected
2. Prevent the server from terminating the connection due to inactivity

## Schema

```json
{
  "type": "heartbeat"
}
```

## Frequency

Clients should send heartbeat messages every 30 seconds to maintain the connection.

## Server Response

The server does not send a response to heartbeat messages. It simply resets the connection's inactivity timer upon receipt.

> [!NOTE]
> If the server doesn't receive a heartbeat message within 90 seconds of the last received message (of any type), it may terminate the connection.

Clients should implement error handling to re-establish the connection if it's terminated.
