# WarningNotifications.jsx

The `WarningNotifications.jsx` component plays a crucial role in the AllyArm application by managing and displaying real-time warning notifications to users. This component ensures that users are promptly alerted to non-critical but potentially important issues, enabling them to take preemptive action before they escalate into more severe problems. Below is a detailed examination of the architecture and inner workings of the `WarningNotifications.jsx` component:

## 1. **Core Imports and Dependencies**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useEffect, useState } from "react";
import { ToastContainer, toast } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";
import { subscribeToMessages, unsubscribeFromMessages } from "../Socket";
import { CiWarning } from "react-icons/ci";
```
{% endtab %}
{% endtabs %}

* **React**: The component leverages the `useEffect` hook to handle side effects, specifically subscribing to and managing incoming warning messages.
* **React Toastify**: The `react-toastify` library is utilized to create customizable toast notifications that alert users to warnings in a visually distinctive manner.
* **Socket Communication**: The component integrates with the WebSocket API through the custom `subscribeToMessages` and `unsubscribeFromMessages` functions, allowing it to receive real-time data updates from the server.
* **Icons**: The `CiWarning` icon from the `react-icons` library is used to visually differentiate warning notifications from other types of messages, using a yellow warning symbol to convey the nature of the alert.

## 2. **Real-Time Data Handling and State Management**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const warningCodes = [];
  const senderWarningCodes = [];
  const notificationWarningIds = [];
```
{% endtab %}
{% endtabs %}

* **Initialization**: The `useEffect` hook initializes arrays to track warning codes, sender identifiers, and unique notification IDs. These arrays are used to manage and prevent duplicate notifications and to track which warnings have been dismissed.

## 3. **Handling Incoming Warning Data**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleDataUpdate = (data) => {
  const { warningData } = data;

  const activeWarningCodes = warningData.map((warning) => warning.warningCode);
  const dismissedWarningIds = [];
  notificationWarningIds.forEach((notificationId) => {
    const warningCode = Number(notificationId.split("-")[1]);
    if (!activeWarningCodes.includes(warningCode)) {
      toast.dismiss(notificationId);
      dismissedWarningIds.push(notificationId);
    }
  });
  dismissedWarningIds.forEach((notificationId) => {
    const index = notificationWarningIds.indexOf(notificationId);
    if (index !== -1) {
      notificationWarningIds.splice(index, 1);
    }
  });
```
{% endtab %}
{% endtabs %}

* **Data Mapping**: The `handleDataUpdate` function processes incoming warning data, mapping out active warning codes to keep track of currently relevant warnings.
* **Warning Dismissal**: The function identifies and dismisses warnings that are no longer active. This is achieved by comparing the currently active warning codes with those that have been previously notified. If a warning is no longer active, its corresponding notification is dismissed, and the ID is removed from the tracking array.

## 4. **Displaying New Warnings**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
warningData.forEach((warning) => {
  const { warningCode, axis, message } = warning;
  const notificationId = `warning-${warningCode}-${axis}`;
  if (!notificationWarningIds.includes(notificationId)) {
    warningCodes.push(warningCode);
    senderWarningCodes.push(axis);

    let nAxis;
    if (axis !== "Master") {
      nAxis = "Axis " + axis;
    } else {
      nAxis = axis;
    }

    toast.warning(`${nAxis}: Warning code ${warningCode}. ${message}`, {
      toastId: notificationId,
      icon: <CiWarning fill="yellow" size="2em" />,
    });

    notificationWarningIds.push(notificationId);
  }
});
```
{% endtab %}
{% endtabs %}

* **Notification Logic**: For each warning in the `warningData`, the component checks if the warning has already been notified by comparing its unique ID (constructed from the warning code and axis) against those already stored. If the warning is new, it is added to the tracking array, and a toast notification is triggered.
* **Message Formatting**: The notification message is dynamically formatted based on the axis associated with the warning. If the axis is not "Master", it is labeled accordingly (e.g., "Axis 1"), ensuring that the user has clear context for the warning.
* **Visual Consistency**: A yellow warning icon (`CiWarning`) is used in each notification to provide visual consistency and immediately signal the nature of the alert to the user.

## 5. **Cleanup and Unsubscription**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleCleanup = () => {
  toast.dismiss();
};

subscribeToMessages(handleDataUpdate);

return () => {
  unsubscribeFromMessages(handleDataUpdate);
  handleCleanup();
};
```
{% endtab %}
{% endtabs %}

* **Subscription Management**: The component subscribes to the WebSocket stream when it mounts, allowing it to receive real-time updates.
* **Cleanup Function**: When the component is unmounted, it unsubscribes from the WebSocket stream and dismisses any active notifications. This cleanup process is essential for preventing memory leaks and ensuring that no obsolete notifications persist after the component has been removed from the DOM.

## 6. **Rendering the Toast Container**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
return (
  <>
    <ToastContainer
      position="bottom-right"
      autoClose={5000}
      hideProgressBar={false}
      newestOnTop
      closeOnClick={false}
      rtl={false}
      pauseOnFocusLoss={false}
      draggable={false}
      closeButton={false}
      theme="colored"
    />
  </>
);
```
{% endtab %}
{% endtabs %}

* **Toast Container Configuration**: The `ToastContainer` component is configured to display notifications in the bottom-right corner of the screen, with the most recent notifications appearing at the top. The auto-close feature ensures that warnings are automatically dismissed after 5 seconds, while the progress bar remains visible to give users a sense of the remaining time.

## 7. **Component Export**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export default WarningNotifications;
```
{% endtab %}
{% endtabs %}

* **Export Statement**: The component is exported as the default export of the module, making it easy to import and integrate into other parts of the application.

{% hint style="info" %}
This detailed explanation highlights the `WarningNotifications.jsx` component's architectural design, emphasizing how it integrates real-time warning management with a responsive and user-friendly notification system. The component ensures that users are promptly informed of non-critical issues, allowing them to maintain control over the system's health and performance.
{% endhint %}
