# Client Unsubscribe Message

The client sends this message to the server to gracefully terminate a connection. It allows for proper cleanup and resource management on the server side.

## Usage

The client should send this message in two scenarios:

1. Before intentionally closing a connection
2. When the client application is shutting down

## Message

```json
{
  "type": "unsubscribe"
}
```

## Server Behavior

Upon receiving an unsubscribe message, the server will:

1. Acknowledge the message
2. Close the connection
3. Release any resources associated with the client's session

### Server Acknowledge Message

```json
{
  "type": "acknowledge",
  "status": "success",
  "timestamp": "2024-06-27T14:30:00Z"
}
```