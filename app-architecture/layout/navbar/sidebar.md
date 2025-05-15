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

# Sidebar

#### Component Breakdown: Sidebar

The **Sidebar** component in your application is a responsive and interactive navigation menu that provides users with quick access to different sections of the app. It is designed to be collapsible and includes access-based conditional rendering, multilingual support, and user-specific information display.

## Key Features

* **Responsive Design**: The sidebar can be toggled open or closed using a hamburger menu icon.
* **Access-Based Conditional Rendering**: Only users with specific access levels can see certain menu items.
* **Multilingual Support**: Users can switch between languages (e.g., English and Italian).
* **User Information Display**: The sidebar shows the current user's name and access level.
* **Logout Functionality**: Users can log out directly from the sidebar.
* **Click Outside to Close**: The sidebar closes automatically if the user clicks outside of it.

## Code Overview

### Importing Dependencies

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useState, useEffect, useRef } from "react";
import Hamburger from "hamburger-react";
import { Link, useNavigate } from "react-router-dom";
import style from "./Sidebar.module.css";
import { MdLogout } from "react-icons/md";
import { useTranslation } from "react-i18next";
import tokenDecode from "../../../Utils/tokenDecode";
```
{% endtab %}
{% endtabs %}

* **React, useState, useEffect, useRef**: Core React functionalities for managing state and lifecycle, and `useRef` for persisting values across renders without causing re-renders.
* **Hamburger**: A component for the hamburger menu icon.
* **Link, useNavigate**: React Router components for navigation.
* **MdLogout**: Icon component for the logout button.
* **useTranslation**: A hook from `react-i18next` for handling translations.
* **tokenDecode**: Utility function for decoding the user's access token, which contains user-specific information like access level and name.

### State and Refs

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [open, setOpen] = useState(false);
const sidebarRef = useRef(null);
const accessLevelRef = useRef(5);
const nameRef = useRef("User");
const navigate = useNavigate();
const { t } = useTranslation();
```
{% endtab %}
{% endtabs %}

* **open**: Manages whether the sidebar is open or closed.
* **sidebarRef**: References the sidebar element to detect clicks outside of it.
* **accessLevelRef, nameRef**: Store the user's access level and name, respectively. These values are initialized with defaults and updated once the token is decoded.
* **navigate**: Allows navigation programmatically.
* **t**: Used for translation strings.

### Handle Click Outside to Close Sidebar

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleClickOutside = (event) => {
  if (sidebarRef.current && !sidebarRef.current.contains(event.target)) {
    setOpen(false);
  }
};

useEffect(() => {
  const storedToken = document.cookie.split(";").find((cookie) => cookie.trim().startsWith("token="));
  const decodedToken = tokenDecode(storedToken.split("=")[1]);
  const storedAccessLevel = decodedToken?.accessLevel || 5;
  const storedName = decodedToken?.name || "User";

  accessLevelRef.current = storedAccessLevel;
  nameRef.current = storedName;

  document.addEventListener("click", handleClickOutside);
  return () => {
    document.removeEventListener("click", handleClickOutside);
  };
}, []);
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **handleClickOutside**: Closes the sidebar if the user clicks outside of it.
* **useEffect**:
  * Decodes the user's token to extract and set the access level and name.
  * Adds an event listener to close the sidebar when clicking outside.
  * Cleans up the event listener on component unmount.

### Logout Handler

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleLogOutClick = () => {
  document.cookie = "token=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";
  navigate("/");
};
```
{% endtab %}
{% endtabs %}

* **handleLogOutClick**: Clears the user's token from cookies and redirects them to the login page.

### Language Change Handler

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const changeLanguage = (e) => {
  const selectedLanguage = e.target.value;
  i18n.changeLanguage(selectedLanguage);
};
```
{% endtab %}
{% endtabs %}

* **changeLanguage**: Changes the application's language when a user selects a different language from the sidebar.

### Sidebar Rendering

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.burger} ref={sidebarRef}>
    <Hamburger toggled={open} className={style.hamburger} toggle={setOpen} />
    {open && (
      <div className={style.burger_menu}>
        {accessLevel >= 5 && (
          <Link to="/home" className={style.burger_menu_item} onClick={handleLinkClick}>
            {t('sidebar.0.home')}
          </Link>
        )}
        {accessLevel >= 5 && (
          <Link to="/wearing" className={style.burger_menu_item} onClick={handleLinkClick}>
           {t('sidebar.0.wearing')}
          </Link>
        )}
        {/* More Links */}
        <div className={style.burger_user_info}>
          <p className={style.burger_user_position}>{getAccessLevelName(accessLevel)}: </p>
          <p className={style.burger_user_name}>{nameRef.current}</p>
        </div>
        <button className={style.burger_logout_logo} type="submit" onClick={handleLogOutClick}>
          <MdLogout size={24} />
        </button>
        <div>
          <button className={style.burger_lang_btn} value="en" onClick={changeLanguage}>
            English
          </button>
          <button className={style.burger_lang_btn} value="it" onClick={changeLanguage}>
            Italiano
          </button>
        </div>
      </div>
    )}
  </div>
);
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Hamburger**: Toggles the sidebar's visibility.
* **Sidebar Menu**: Conditionally renders links based on the user's access level. Links to different sections of the application.
* **User Info and Logout**: Displays the user's role and name, with an option to log out.
* **Language Selection**: Provides buttons to switch between English and Italian.

## Summary

The **Sidebar** component adds an interactive and responsive menu that adapts to the user's role and preferences. It features access control, multilingual support, and user-specific details, making it a powerful tool for navigating the application while maintaining a clean and user-friendly interface. This component is crucial for enhancing the user experience, particularly on devices where screen space is limited.
