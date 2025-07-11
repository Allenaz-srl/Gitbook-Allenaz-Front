# SharedLayout.jsx

The `SharedLayout.jsx` file serves as a layout component that provides a consistent structure across different pages of the AllyArm application. It includes common elements such as the navigation bar and notification components that are displayed on all pages. The code in this file can be divided into the following key sections:

## 1. **Core Imports**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import { Outlet } from "react-router-dom";
import { Navbar } from "./Layout/Navbar/Navbar";
import ErrorNotifications from "./Notification/ErrorNotifications";
import WarningNotifications from "./Notification/WarningNotifications";
import style from "./App.module.css";
```
{% endtab %}
{% endtabs %}

* **React Router**: The `Outlet` component from `react-router-dom` is imported to render nested routes. It acts as a placeholder where the child routes defined in the main `App` component will be rendered.
* **Navigation Bar**: The `Navbar` component is imported, which provides navigation links for the application.
* **Notification Components**: The `ErrorNotifications` and `WarningNotifications` components are imported to display error and warning messages to users across all pages.
* **CSS Module**: The `App.module.css` file is imported to apply specific styles to elements within the layout.

## 2. **Layout Structure**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export const SharedLayout = () => {
  return (
    <>
      <Navbar />
      <main className={style.main_sec}>
        <Outlet />
      </main>
      <ErrorNotifications />
      <WarningNotifications />
    </>
  );
};
```
{% endtab %}
{% endtabs %}

* **Navbar**: The `Navbar` component is rendered at the top of the layout, providing consistent navigation across different pages.
* **Main Section**: The `main` section, styled with `style.main_sec`, is the primary content area where different components and pages will be displayed. The `Outlet` component within this section dynamically renders the content of the current route.
* **Notifications**: The `ErrorNotifications` and `WarningNotifications` components are placed outside the `main` section, ensuring that they are displayed consistently across all pages, regardless of the current route.

## 3. **Component Composition**

* **Wrapper Elements**: The layout is wrapped in a React fragment (`<>...</>`), allowing multiple JSX elements to be returned without adding extra nodes to the DOM.
* **Content and Notifications Separation**: The structure separates the main content (rendered via the `Outlet`) from the notifications, ensuring that alerts and navigation are always present, but do not interfere with the primary content area.
