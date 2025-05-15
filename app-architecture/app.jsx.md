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

# App.jsx

The `App.jsx` file serves as the central hub of the AllyArm application, orchestrating the overall structure, navigation, and user authentication. It brings together various components and modules, ensuring that the application functions seamlessly. The code in this file can be grouped and understood in the following key sections:

## 1. **Imports and Lazy Loading**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useState, useEffect, lazy, Suspense } from "react";
import { Routes, Route, useLocation, useNavigate } from "react-router-dom";
import style from "./App.module.css";
import { SharedLayout } from "./SharedLayout";
import tokenDecode from "./Utils/tokenDecode";
import { sendMessage } from "./Socket";
```
{% endtab %}
{% endtabs %}

* **Core Imports**: React and related hooks (`useState`, `useEffect`) are imported alongside routing utilities (`useLocation`, `useNavigate`).
* **Lazy Loading**: Components are lazy-loaded using `React.lazy()`, which optimizes the application's performance by splitting the code and loading components only when they are needed.
* **Utility Imports**: The `tokenDecode` function decodes JWT tokens, and `sendMessage` manages WebSocket communication with the backend.

## 2. **State and Navigation Setup**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const location = useLocation();
const storedToken = document.cookie.split(";").find((cookie) => cookie.trim().startsWith("token="));
const storedAccessLevel = storedToken ? tokenDecode(storedToken.split("=")[1])?.accessLevel : 1;
const [accessLevel, setAccessLevel] = useState(storedAccessLevel);
const navigate = useNavigate();
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **State Initialization**: The `accessLevel` state is initialized based on the decoded token stored in cookies. This state determines what parts of the application the user can access.
* **Navigation**: The `useNavigate` hook is used for programmatic navigation, ensuring users are redirected based on their authentication status.

## 3. **Effect Hooks for State Management**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  setAccessLevel(storedAccessLevel);
}, [storedAccessLevel, location]);

useEffect(() => {
  if (!storedToken) {
    navigate("/");
  }
}, [storedToken, navigate]);
```
{% endtab %}
{% endtabs %}

* **Access Level Management**: The first `useEffect` ensures that the `accessLevel` state is updated whenever the token changes or the user navigates to a different page.
* **Authentication Check**: The second `useEffect` monitors the presence of a valid token and redirects unauthenticated users to the login page.

## 4. **Role-Based Routing**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const isAdmin = accessLevel >= 20;
const isAdvance = accessLevel >= 15;
const isBase = accessLevel >= 5;
```
{% endtab %}
{% endtabs %}

* **Role Definitions**: Based on the decoded token, users are classified into roles (e.g., `isAdmin`, `isAdvance`, `isBase`), which control access to different routes within the application.

## 5. **Main Application Structure**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
return (
  <div className={style.App}>
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<LoginForm />} />
        <Route path="/registration" element={<Registration />} />
        <Route path="/" element={<SharedLayout />}>
          {/* Nested Routes based on Access Level */}
        </Route>
      </Routes>
    </Suspense>
  </div>
);
```
{% endtab %}
{% endtabs %}

* **Component Wrapping**: The entire application is wrapped in a `<Suspense>` component, providing a fallback UI while lazy-loaded components are being fetched.
* **Routing**: The `<Routes>` component defines the application’s navigation structure, with routes nested inside a shared layout. Access to these routes is conditional, depending on the user's `accessLevel`.

## 6. **Conditional Rendering Based on Access Levels**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
{isBase && <Route path="/home" element={<Home />} />}
{isAdvance && <Route path="/testing" element={<TestMode />} />}
{isAdmin && /* More advanced routes for admin users */}
```
{% endtab %}
{% endtabs %}

* **Access Control**: Specific routes are only rendered if the user has the necessary access level. This ensures that users only see and interact with components appropriate for their role.
