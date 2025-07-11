---
description: Dependencies Overview
---

# Dependencies

{% hint style="info" %}
This section provides an overview of the key dependencies used in the AllyArm application. These libraries are essential for various functionalities, ranging from UI development and state management to testing and backend communication. Below is a categorized list of dependencies with brief descriptions of their roles in the application.
{% endhint %}

## 1. **UI/UX and Styling**

These dependencies are used to create and style the user interface of the AllyArm application, ensuring a modern and responsive design.

* **@emotion/react (v. 11.11.1)**: Library for writing CSS styles with JavaScript.
* **@emotion/styled (v. 11.10.6)**: Styled-components inspired styling library built on Emotion.
* **@mui/material (v. 5.13.3)**: A popular React UI framework that provides pre-built components with Material Design.
* **framer-motion (v. 10.12.22)**: A library for creating smooth animations in React.
* **react-awesome-reveal (v. 4.2.5)**: Simple way to create reveal animations.
* **react-burger-menu (v. 3.0.9)**: Component for creating sidebar menus.
* **react-feather (v. 2.0.10)**: A collection of simply beautiful open source icons for React.
* **react-icons (v. 4.8.0)**: Include popular icons in your React projects easily.
* **react-slider (v. 2.0.4)**: A slider component for React.
* **react-toastify (v. 9.1.3)**: A library for displaying customizable toast notifications.
* **reapop (v. 4.2.1)**: A customizable system for managing notifications in React applications.
* **react-type-animation (v. 3.2.0)**: A library for creating typewriter-style animations.

## 2. **State Management** <a href="#id-2.-state-management" id="id-2.-state-management"></a>

These libraries help manage the state of the application, making it easier to handle complex data flows and maintain consistency across components.

* **@reduxjs/toolkit (v. 1.9.5)**: The official, recommended way to write Redux logic, reducing boilerplate.
* **react-redux (v. 8.1.1)**: Official React bindings for Redux.
* **redux (v. 4.2.1)**: A predictable state container for JavaScript apps.

## 3. **Routing** <a href="#id-3.-routing" id="id-3.-routing"></a>

Routing libraries manage the navigation between different views in the application.

* **react-router-dom (v. 6.10.0)**: A standard routing library for React applications, enabling dynamic routing.
* **react-router-prompt (v. 0.5.4)**: A React component used to prompt the user before navigating away from a page.

## 4. Drag-and-Drop <a href="#id-4.drag-and-drop" id="id-4.drag-and-drop"></a>

These libraries are used to implement drag-and-drop functionality within the application.

* **@hello-pangea/dnd (v. 16.3.0)**: A robust drag-and-drop library for React.
* **react-beautiful-dnd (v. 13.1.1)**: Another popular drag-and-drop library focused on beautiful and accessible drag-and-drop experiences.
* **react-dnd (v. 16.0.1)**: A flexible and extensible drag-and-drop library for React.
* **react-dnd-html5-backend (v. 16.0.1)**: The HTML5 backend for React DnD.
* **react-draggable (v. 4.4.5)**: A library for making elements draggable in React.
* **react-sortablejs (v. 6.1.4)**: A React wrapper for SortableJS, which makes any list sortable.

## 5. **Testing** <a href="#id-5.testing" id="id-5.testing"></a>

Testing libraries ensure the application works as expected and helps catch bugs before production.

* **@testing-library/jest-dom (v. 5.16.3)**: Custom jest matchers to test the state of the DOM.
* **@testing-library/react (v. 12.1.4)**: Simple and complete testing utilities for React components.
* **@testing-library/user-event (v. 13.5.0)**: Simulates user events for React testing.

## 6. **Internationalization** <a href="#id-6.-internationalization" id="id-6.-internationalization"></a>

These dependencies support the internationalization (i18n) features in the application, making it accessible to a global audience.

* **i18next (v. 23.2.11)**: An internationalization framework for JavaScript.
* **react-i18next (v. 13.0.2)**: React bindings for i18next.

## 7. **Authentication and Security**

Libraries used to handle authentication and ensure secure communication.

* **jsonwebtoken (v. 9.0.1)**: Library for working with JSON Web Tokens (JWT), used for authentication.
* **jwt-decode (v. 3.1.2)**: A small library that helps decode JWT tokens.

## 8. **Networking and APIs**

These libraries are essential for making network requests and handling data communication between the frontend and backend.

* **axios (v. 1.3.4)**: A promise-based HTTP client for making requests to the backend.
* **socket.io (v. 4.7.2)**: Library for real-time web applications, used for implementing WebSocket communication.
* **socket.io-client (v. 4.7.2)**: The client-side library for connecting to a Socket.io server.
* **socket.io-stream (v. 0.9.1)**: Adds streaming capabilities to Socket.io.

## 9. **Utilities**

Various utility libraries that provide additional functionalities and support for different tasks in the application.

* **buffer (v. 6.0.3)**: A library for working with binary data in JavaScript.
* **cookie (v. 0.5.0)**: A simple library for handling cookies.
* **cors (v. 2.8.5)**: A middleware for enabling Cross-Origin Resource Sharing in your server.
* **crypto-browserify (v. 3.12.0)**: A cryptography library for browsers.
* **dotenv (v. 16.3.1)**: Loads environment variables from a `.env` file into `process.env`.
* **js-cookie (v. 3.0.5)**: A JavaScript API for handling cookies.
* **moment (v. 2.29.4)**: A library for parsing, validating, manipulating, and formatting dates.
* **nodemon (v. 2.0.22)**: A utility that automatically restarts the Node.js application when file changes are detected.
* **stream-browserify (v. 3.0.0)**: A stream library for browsers, used as a polyfill for Node.js streams.
* **util (v. 0.12.5)**: A collection of utility functions for Node.js.
