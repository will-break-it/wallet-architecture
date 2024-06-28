# Push-based API Overview

The following serves as introduction to the wallet-optimized, event-driven API using **websockets**.
This document outlines the structure and common elements of messages that clients may process.

> ℹ️ **Remark**<br />
> New message types can be added at any time.
> Clients are expected to ignore messages they do not support.

## Protocol

We use a bidirectional protocol and encode all messages as JSON objects. Messages adhere to a specific structure, ensuring consistency and facilitating efficient communication within our event-driven protocol.

Each message has a `type` attribute that can be used to handle and distinguish messages appropriately. Common types include `subscribe`, `heartbeat`, `transaction` etc. a complete list is available [here](./messages/index.md).

The `data` object contains the actual payload of the message, with fields that vary based on the message type.

```json
{
  "type": "unique_message_type",
  "data": {
    // event-specific data fields
  }
  /* ... */
}
```

### Error Handling

Any cases of failure trigger an `error` message.

```json
{
  "type": "error",
  "message": "error message"
  /* ... */
}
```

## Message Types

We distinguish between client-sent and server-sent messages. Both define a `type` as described above, but server-sent message define additional fields that are detailed [here](./messages/index.md).

## Keep Alive/ Heartbeat Messages

To conserve server resources, we shift connection maintenance responsibility to clients. This allows the server to terminate any inactive connections not regularly refreshed by clients.

More information can be found [here](./messages/client/heartbeat.md).

## Authentication

API authentication in its first version is implicitly handled by the client specifying the topics of interest in their initial [`subscribe`](./messages/client/subscribe.md) message. This message must include a **signature** that verifies the ownership of the provided credentials, which are used to filter block transactions.

More information can be found [here](./messages/client/subscribe.md).