# Navbar

#### Component Breakdown: Navbar

The **Navbar** component is a central navigation hub in the application, providing access to different sections based on the user's access level. The navigation bar is designed to be dynamic, showing different options depending on the user's privileges. Additionally, the component includes several features such as a notification center, battery status display, and a sidebar for additional navigation options.

## Key Features

* **Dynamic Navigation**: The component renders different navigation links based on the user's access level.
* **Battery Status Display**: A visual indicator shows the battery status of the exoskeleton.
* **Notification Center**: The component integrates a notification system to alert the user of important events.
* **User Information**: Displays the logged-in user's role and name.
* **Sidebar Integration**: Includes a sidebar for additional navigation options.
* **WebSocket Communication**: Handles real-time data updates and communication with the backend.

## Code Overview

### Importing Dependencies

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
import React, { useState, useEffect } from "react";
import style from "./Navbar.module.css";
import { MdLogout } from "react-icons/md";
import { Link } from "react-router-dom";
import { Sidebar } from "./Sidebar/Sidebar";
import { subscribeToMessages, sendMessage, unsubscribeFromMessages } from "../../Socket";
import { NotificationCenter } from "../../Notification/NotificationsCenter";
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **React, useState, useEffect**: Core React functionalities for state management and lifecycle handling.
* **MdLogout**: An icon component for the logout button.
* **Link**: Used for client-side routing with React Router.
* **Sidebar**: Custom sidebar component for additional navigation.
* **WebSocket Functions**: Functions for subscribing to and sending messages over WebSocket.
* **NotificationCenter**: Component for displaying notifications.

### State Management and Effects

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [data, setData] = useState({});
const [accessLevel, setAccessLevel] = useState(20); // Default level set to 20
const [buttonPresses, setButtonPresses] = useState({});
```
{% endtab %}
{% endtabs %}

* **data**: Stores the latest data received via WebSocket, such as battery status.
* **accessLevel**: Determines what navigation items are displayed based on the user's access level. The default is set to 20 (admin level).
* **buttonPresses**: Keeps track of button presses that are sent to the backend.

### useEffect: Handling Data and Access Level

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setData(newData);
  };

  subscribeToMessages(handleDataUpdate);

  const savedAccessLevel = Number(localStorage.getItem("accessLevel"));
  if (savedAccessLevel) {
    setAccessLevel(savedAccessLevel);
  }

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);
```
{% endtab %}
{% endtabs %}

* **Data Subscription**: Subscribes to data updates via WebSocket when the component mounts. This is essential for keeping the UI responsive to real-time changes (e.g., battery status).
* **Access Level Retrieval**: Retrieves the user's access level from `localStorage` to ensure persistent state across sessions.
* **Cleanup**: Unsubscribes from WebSocket messages when the component unmounts to prevent memory leaks.

### Handling Button Presses

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleButton = (buttonId) => {
  const buttonPress = [buttonId];

  setButtonPresses((prevButtonPresses) => ({
    ...prevButtonPresses,
    ...buttonPress,
  }));

  sendMessage(buttonPress);
  console.log(buttonPress);
};
```
{% endtab %}
{% endtabs %}

* **Button Press Logic**: Captures the ID of the pressed button and sends it to the backend. This is used to trigger specific actions, such as stopping the exoskeleton arm.

### Rendering the Navigation Bar

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.navigation}>
    <header className={style.header_navbar}>
      <div>
        <h1 className={style.header_title}>
          <Link style={{ color: "inherit", textDecoration: "inherit" }} to="/home">
            Ally<span className={style.logo_title_color}>Arm</span>
          </Link>
        </h1>
        <button type="" className={style.home_btn_stop} onClick={() => handleButton("stop")}>
          STOP
        </button>
      </div>
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Logo and Home Link**: Displays the application logo and links to the home page.
* **Stop Button**: A button to stop the exoskeleton, triggering the `handleButton` function with the "stop" ID.

### Notification Center and Battery Status

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<div className={style.notification_center}>
  <NotificationCenter />
</div>
<div className={style.battery} id="battery">
  <span className={style.battery_value} id="battery-value">
    {data.batteryStatus}%
  </span>
  <div className={style.battery_level} id="battery-level" style={{ width: data.batteryStatus + "%" }}></div>
</div>
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Notification Center**: Integrates the `NotificationCenter` component, which manages and displays notifications.
* **Battery Status**: Displays the current battery status as a percentage and a visual bar.

### Dynamic Navigation Links

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<nav className={style.subnavigation}>
  <ul className={style.sub_list}>
    {accessLevel >= 5 && (
      <li className={style.sub_item}>
        <Link to="/wearing" className={style.sub_item_link}>
          Wearing
        </Link>
      </li>
    )}
    {accessLevel >= 5 && (
      <li className={style.sub_item}>
        <Link to="/exercises" className={style.sub_item_link}>
          Exercises
        </Link>
      </li>
    )}
    {accessLevel >= 15 && (
      <li className={style.sub_item}>
        <Link to="/errorwarning" className={style.sub_item_link}>
          Error/Warning
        </Link>
      </li>
    )}
    {accessLevel >= 20 && (
      <li className={style.sub_item}>
        <Link to="/testing" className={style.sub_item_link}>
          Test
        </Link>
      </li>
    )}
    {accessLevel >= 5 && (
      <li className={style.sub_item}>
        <Link className={style.sub_item_link} to="/setting">
          Setting
        </Link>
      </li>
    )}
    {accessLevel >= 5 && (
      <li className={style.sub_item}>
        <Link to="/diagnostics" className={style.sub_item_link}>
          Diagnostics
        </Link>
      </li>
    )}
  </ul>
</nav>
```
{% endtab %}
{% endtabs %}

* **Access-Based Links**: Renders different navigation options depending on the user's access level. For example, users with an access level of 20 or higher can see the "Test" link, while users with a level of 5 can see "Wearing", "Exercises", etc.

### User Information and Logout

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<div className={style.header_user_info}>
  <p className={style.header_user_position}>Admin: </p>
  <p className={style.header_user_name}>Roman</p>
</div>
<Link style={{ color: "inherit", textDecoration: "inherit" }} to="/">
  <button className={style.header_logout_logo} type="submit">
    <MdLogout size={24} />
  </button>
</Link>
<div className={style.sidebar}>
  <Sidebar />
</div>
```
{% endtab %}
{% endtabs %}

* **User Info**: Displays the user's role and name.
* **Logout Button**: Provides a logout button that redirects the user to the login page.
* **Sidebar**: Includes the `Sidebar` component for additional navigation options.

## Summary

The **Navbar** component is a sophisticated and dynamic navigation tool that adapts to the user's role and access level, providing the appropriate level of access to various sections of the application. It handles real-time updates, user interactions, and session management, making it a key element of the application’s user interface. The next step would be to examine the **Sidebar** component in more detail.
