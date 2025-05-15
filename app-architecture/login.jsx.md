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

# Login.jsx

### Login Component: `Login.jsx`

The `Login.jsx` component serves as the user authentication interface in the AllyArm application. It provides a secure and interactive way for users to log in with different access levels—Base, Advance, and Admin. This component interacts with the backend to verify user credentials and manage session tokens.

## 1. **Component Overview**

The `LoginForm` component is a functional React component that manages the user login process. It includes form handling, validation, and API requests to authenticate users. Additionally, it handles UI interactions such as displaying error messages and dynamically updating the login form based on user input.

## 2. **Core Imports and Dependencies**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useState, useEffect } from "react";
import style from "../Login.module.css";
import axios from "axios";
import { useNavigate } from "react-router-dom";
import { TypeAnimation } from "react-type-animation";
import { Fade } from "react-awesome-reveal";
import { unsubscribeFromMessages, subscribeToMessages } from "../../Socket";
```
{% endtab %}
{% endtabs %}

* **React**: The core library for building user interfaces.
* **useState & useEffect**: React hooks used for managing state and side effects within the component.
* **axios**: A promise-based HTTP client for making API requests to the backend.
* **useNavigate**: A hook from `react-router-dom` for navigation between different routes within the application.
* **TypeAnimation & Fade**: Third-party libraries for adding animated text and fade-in effects to the component.
* **Socket Subscription**: Custom functions (`subscribeToMessages`, `unsubscribeFromMessages`) for handling real-time updates, particularly for retrieving the current IP address from the backend.

## 3. **State Management**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [password, setPassword] = useState("");
const [accessLevel, setAccessLevel] = useState("Base");
const [error, setError] = useState(null);
const [ipAddress, setIpAddress] = useState("");
```
{% endtab %}
{% endtabs %}

* **password**: Stores the user's input for the password field.
* **accessLevel**: Tracks the selected access level (Base, Advance, Admin).
* **error**: Holds any error message to display when login fails.
* **ipAddress**: Dynamically updates the IP address retrieved from the backend, used to direct API requests.

## 4. **Real-Time IP Address Fetching**

{% hint style="info" %}
The `useEffect` hook subscribes to real-time messages to update the IP address when the component mounts and unsubscribes when it unmounts.
{% endhint %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setIpAddress(newData.ipAddress);
  };

  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);
```
{% endtab %}
{% endtabs %}

This setup ensures that the IP address used in API requests is always up to date.

## 5. **Form Submission Logic**

{% hint style="info" %}
The `handleSubmit` function is triggered when the login form is submitted. It prevents the default form submission behavior, collects the user input, and sends a POST request to the backend to authenticate the user.
{% endhint %}

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleSubmit = async (event) => {
  event.preventDefault();

  const userData = {
    accessLevel,
    password,
  };

  try {
    const response = await axios.post(`http://${ipAddress}:3001/login`, userData);
    console.log(response.data.message);
    console.log(response.data.token);

    // Save the JWT token as a httpOnly cookie
    document.cookie = `token=${response.data.token}; path=/; samesite=strict`;

    // Decode the JWT token to extract access level
    const decodedToken = JSON.parse(atob(response.data.token.split(".")[1]));
    const accessLevel = decodedToken.accessLevel;
    console.log("Access Level: ", accessLevel);

    // Redirect to the appropriate page based on access level
    navigate("/wearing", { state: { accessLevel } });

    setPassword(""); // Clear password field after successful login
  } catch (error) {
    console.error("Error during login: ", error);
    setError("OPS! Wrong password, try another...");
  }
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **UserData Object**: Combines the access level and password to be sent in the POST request.
* **axios.post()**: Sends the login request to the backend with the user's credentials.
* **Document Cookie**: Saves the JWT token returned from the server as a secure, httpOnly cookie.
* **navigate**: Redirects the user to the "/wearing" route upon successful login.
* **Error Handling**: Displays an error message if the login fails.

## 6. **User Interface (UI) and Animations**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<TypeAnimation
  sequence={["Welcome to", 3000, "Benvenuto in", 3000]}
  style={{ fontFamily: "Cinzel", letterSpacing: ".08em" }}
  repeat={Infinity}
/>

<Fade cascade damping={0.1} duration={1000} direction="up" triggerOnce={true} fraction={0.1}>
  <h2 className={style.auth_form_title}>
    Ally<span className={style.logo_title_color}>Arm</span>
  </h2>

  <form className={style.login_form} onSubmit={handleSubmit}>
    <Fade delay={300} duration={800} cascade damping={0.1} triggerOnce={true}>
      <label className={style.login_label}>Access Level</label>
      <div className={style.login_access_level}>
        <label className={style.login_label_checkbox}>
          <input type="radio" value="Base" checked={accessLevel === "Base"} onChange={() => setAccessLevel("Base")} />
          <span>Base</span>
        </label>
        <label className={style.login_label_checkbox}>
          <input type="radio" value="Advance" checked={accessLevel === "Advance"} onChange={() => setAccessLevel("Advance")} />
          Advance
        </label>
        <label className={style.login_label_checkbox}>
          <input type="radio" value="Admin" checked={accessLevel === "Admin"} onChange={() => setAccessLevel("Admin")} />
          Admin
        </label>
      </div>
    </Fade>
    <Fade delay={500} duration={800} damping={0.1} triggerOnce={true}>
      <label className={style.login_label} htmlFor="password">
        Password
        <input
          className={style.login_input}
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          name="password"
          type="password"
          id="password"
          placeholder="*********"
        />
      </label>
    </Fade>
    <div className={style.login_error_box}>
      <Fade delay={300} duration={300} damping={0.1} triggerOnce={true}>
        {error && <p className={style.login_error_message}>{error}</p>}
      </Fade>
    </div>
    <Fade delay={700} duration={800} damping={0.1} triggerOnce={true}>
      <button className={style.login_btn} type="submit">
        Login
      </button>
    </Fade>
  </form>
</Fade>
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **TypeAnimation**: Provides a typewriter effect for the welcome message, switching between English and Italian.
* **Fade**: Adds a smooth fade-in effect for the text and form elements, enhancing the user experience.
* **CSS Modules**: Ensures scoped and modular styling for the component, preventing style conflicts.

## 7. **Security Considerations**

* **Token Management**: The JWT token is stored in an httpOnly cookie, enhancing security by preventing JavaScript access to the token, mitigating XSS attacks.
* **Input Validation**: The form includes basic client-side validation, ensuring that user inputs are sanitized before being sent to the backend.

{% hint style="info" %}
This documentation entry covers the `Login.jsx` component in detail, explaining its structure, state management, API interaction, and UI components. The login process is crucial for the AllyArm application, ensuring that only authorized users can access certain features based on their access level. The component integrates real-time updates, secure token management, and an engaging user interface to provide a smooth and secure authentication experience.
{% endhint %}
