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

# NotificationsCenter.jsx

The `NotificationsCenter.jsx` file is a crucial component in the AllyArm application that manages the display and interaction of real-time notifications related to system errors and warnings. This component uses state management, WebSocket communication, and animation techniques to provide a dynamic and responsive notification system. Here's an overview of its architecture and functionality:

## 1. **Core Imports and Dependencies**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
import React, { useState, useEffect, useRef } from "react";
import { motion, AnimatePresence } from "framer-motion";
import { useNotificationCenter } from "react-toastify/addons/use-notification-center";
import style from "./NotificationCenter.module.css";
import { sendMessage, subscribeToMessages, unsubscribeFromMessages } from "../Socket";
import { useTranslation } from "react-i18next";
import Trigger from "./NotificationCenter/Trigger";
import { OutsideClick } from "./NotificationCenter/OutsideClick";
import { Dialog, DialogActions, DialogContent, DialogTitle, Button } from "@mui/material";
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **State and Effects**: React's `useState`, `useEffect`, and `useRef` hooks are utilized for managing state, side effects, and references to DOM elements.
* **Animation**: `motion` and `AnimatePresence` from `framer-motion` are used to create smooth animations for displaying notifications.
* **Notification Management**: `useNotificationCenter` from `react-toastify` helps manage notification state and behavior, including unread counts and clearing notifications.
* **WebSocket Communication**: Custom functions from `Socket.jsx` handle real-time communication with the backend, allowing notifications to be pushed and handled dynamically.
* **Localization**: `useTranslation` from `react-i18next` is integrated to support multiple languages, making the app adaptable to different regions.
* **Material UI**: Components like `Dialog`, `DialogActions`, `DialogContent`, `DialogTitle`, and `Button` from Material UI are used to create modals for displaying detailed notification information.

## 2. **State Management**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const [notifications, setNotifications] = useState([]);
const { notifications: socketNotifications, clear, unreadCount } = useNotificationCenter();
const [showUnreadOnly, setShowUnreadOnly] = useState(false);
const [isOpen, setIsOpen] = useState(false);
const [buttonPresses, setButtonPresses] = useState({});
const [data, setData] = useState([]);
const [isModalOpen, setIsModalOpen] = useState(false);
const [selectedError, setSelectedError] = useState(null);
const [selectedWarning, setSelectedWarning] = useState(null);
const [showDescription, setShowDescription] = useState(false);
const [showCause, setShowCause] = useState(false);
const [showSolution, setShowSolution] = useState(false);
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Notifications**: Manages the list of current notifications and updates when new data is received.
* **Modal State**: Handles the opening and closing of the modal that shows detailed information for a selected notification.
* **Filtering**: Allows filtering of notifications to show only unread ones, improving user focus on unresolved issues.
* **Error/Warning Handling**: Manages the selection and display of detailed information for specific errors or warnings, including descriptions, causes, and solutions.

## 3. **WebSocket Integration**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setData(newData);
  };

  subscribeToMessages(handleDataUpdate);
  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);

useEffect(() => {
  setNotifications(socketNotifications);
  console.log("NOTIFICATIONS: ", socketNotifications);
}, [socketNotifications]);
```
{% endtab %}
{% endtabs %}

* **Real-Time Data Handling**: The component subscribes to messages from the backend, updating the state whenever new data is received. This ensures that the notifications are always up to date with the current state of the system.
* **Cleanup**: The WebSocket subscription is cleaned up when the component unmounts, preventing memory leaks and ensuring efficient resource use.

## 4. **Notification Display and Interaction**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleRowClick = (notification) => {
  const idNumber = parseInt(notification.id.match(/\d+/)[0], 10);

  if (notification.type === "warning") {
    const matchingWarning = data.warningData.find((warning) => warning.warningCode === idNumber);
    if (matchingWarning) {
      setSelectedError(null);
      setSelectedWarning(matchingWarning);
      setShowDescription(matchingWarning.description);
      setShowCause(matchingWarning.cause);
      setShowSolution(matchingWarning.solution);
    } else {
      console.log("No matching warning found");
    }
  } else if (notification.type === "error") {
    const matchingError = data.errorData.find((error) => error.errorCode === idNumber);
    if (matchingError) {
      setSelectedWarning(null);
      setSelectedError(matchingError);
      setShowDescription(matchingError.description);
      setShowCause(matchingError.cause);
      setShowSolution(matchingError.solution);
    } else {
      console.log("No matching error found");
    }
  } else {
    console.log(notification);
  }
  setIsModalOpen(true);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Notification Selection**: When a user clicks on a notification, the application parses the notification's ID and retrieves the corresponding error or warning details from the state. This information is then displayed in a modal dialog.
* **Conditional Rendering**: The modal content is conditionally rendered based on whether an error or a warning is selected, ensuring the correct details are presented.

## 5. **UI and Animation**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<Trigger onClick={() => setIsOpen(!isOpen)} count={unreadCount} />
{isOpen && (
  <motion.aside
    className={style.notification_center_container}
    initial={false}
    variants={variants.container}
    animate={isOpen ? "open" : "closed"}
  >
    ...
  </motion.aside>
)}
```
{% endtab %}
{% endtabs %}

* **Trigger Component**: The `Trigger` component handles the opening and closing of the notification center, displaying the count of unread notifications.
* **Animated Sidebar**: The notification center is displayed as an animated sidebar, using `motion` and `AnimatePresence` from `framer-motion` to manage the opening and closing transitions.

## 6. **Modal for Notification Details**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<Dialog
  open={isModalOpen}
  onClose={handleModalNoClick}
  sx={{
    "& .MuiPaper-root": {
      background: "#4d494d",
    },
  }}
>
  <DialogTitle sx={{ color: "rgb(0, 165, 255)" }}>Details</DialogTitle>
  <DialogContent sx={{ color: "white" }}>
    ...
  </DialogContent>
  <DialogActions sx={{ display: "flex", justifyContent: "center", alignItems: "center" }}>
    <Button onClick={handleModalNoClick} color="primary">
      <p className={style.error_warning_btn_reset}>OK</p>
    </Button>
  </DialogActions>
</Dialog>
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Dialog Component**: The modal dialog from Material UI displays detailed information about the selected error or warning. It is styled to match the application's theme and provides a user-friendly interface for viewing and acknowledging notifications.

{% hint style="info" %}
This detailed breakdown explains the structure, logic, and functionality of the `NotificationsCenter.jsx` component. Each section highlights the critical aspects of how notifications are managed, displayed, and interacted with within the AllyArm application.
{% endhint %}
