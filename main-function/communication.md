---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Communication

## Communication between front and back (SOCKET)

In the AllyArm application, communication between the frontend and backend is facilitated through WebSocket. This allows for real-time data exchange, ensuring that the frontend and backend remain synchronized without the need for constant polling.

The WebSocket connection is established and managed in the `socket.jsx` file, which contains several key functions used throughout the application for sending and receiving data.

## WebSocket Connection Setup

The connection to the backend is initialized using the following code:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import { io } from "socket.io-client";

const socket = io(":3001/", { transports: ["websocket", "polling"] });
```
{% endtab %}
{% endtabs %}

* **Socket Initialization**: The `io()` function from `socket.io-client` is used to connect to the backend server on port `3001`. The connection supports both `websocket` and `polling` transports to ensure compatibility across different environments.

## Version Check

One of the first actions taken after establishing the connection is the immediate check of the frontend version against the backend. This is done by sending the frontend version information to the backend:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
let versioneHMIFront = { 1: "1.7.8" };
sendMessage(versioneHMIFront);
```
{% endtab %}
{% endtabs %}

* **Version Check**: The `versioneHMIFront` object contains the current version of the frontend (`1.7.8`). This version is sent to the backend as soon as the connection is established. The backend then compares this version with its own to ensure compatibility.

## Sending and Receiving Messages

The `socket.jsx` file includes functions for sending and receiving data between the frontend and backend, which are crucial for maintaining the application's real-time features:

&#x20; **1.Sending Messages**:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export function sendMessage(changedValues) {
  socket.emit("response", changedValues);
  console.log("Send to server:", changedValues);
}
```
{% endtab %}
{% endtabs %}

* The `sendMessage` function is used to send data from the frontend to the backend. This function can be invoked with any data that needs to be transmitted, and it will be sent via the established WebSocket connection.

&#x20; **2.Subscribing to Messages**:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export function subscribeToMessages(callback) {
  dataCallback = callback;
  socket.on("data", dataCallback);
}
```
{% endtab %}
{% endtabs %}

* The `subscribeToMessages` function allows the frontend to listen for incoming messages from the backend. When data is received, the specified callback function is invoked, processing the received data.

&#x20; 3.**Unsubscribing from Messages**:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export function unsubscribeFromMessages() {
  if (dataCallback) {
    socket.off("data", dataCallback);
    dataCallback = null;
  }
}
```
{% endtab %}
{% endtabs %}

* The `unsubscribeFromMessages` function is used to stop listening for incoming messages from the backend. This can be useful when the component that was handling the messages is unmounted or no longer needs to receive updates.

## Application-Wide Usage

These WebSocket functions (`sendMessage`, `subscribeToMessages`, and `unsubscribeFromMessages`) are utilized across various parts of the AllyArm application. They form the backbone of real-time communication, enabling the frontend to send data to the backend (e.g., user inputs, configuration changes) and receive data from the backend (e.g., system status, diagnostic results).

This architecture ensures that the frontend and backend are always in sync, providing a responsive and interactive user experience.
