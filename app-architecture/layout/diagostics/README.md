# Diagostics

#### App Architecture: Diagnostics Component

The **Diagnostics** component is a crucial part of the application, providing users (typically technicians or engineers) with diagnostic information about the exoskeleton arm. This section enables the monitoring and assessment of the device's performance, including detailed insights into the system's axes. Additionally, the component tracks when the diagnostics section is accessed, sending this information to the backend for monitoring purposes.

## Overview

The **Diagnostics** component is structured around the following key functionalities:

1. **Axes Information**: The component incorporates the **AxesMenu** subcomponent, which displays detailed information about the various axes of the exoskeleton arm.
2. **User Activity Tracking**: Each time a user navigates to the diagnostics section, a message is sent to the backend, indicating that this section is being accessed. This helps in tracking how often diagnostics are being checked, which can be valuable for maintenance records and user analytics.
3. **Resource Management**: When the component is unmounted, any active subscriptions or connections are cleaned up to prevent memory leaks and ensure efficient resource usage.

## Component Breakdown

Let's explore the **Diagnostics** component in detail:

### Importing Dependencies

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useEffect } from "react";
import style from "./Diagnostics.module.css";
import AxesMenu from "./AxesMenu";
import { sendMessage, unsubscribeFromMessages } from "../../Socket";
```
{% endtab %}
{% endtabs %}

* **React and useEffect**: React is used for rendering the UI, and the `useEffect` hook is employed for managing side effects, such as sending messages and cleaning up subscriptions.
* **Styling**: The component is styled using a CSS module (`Diagnostics.module.css`), ensuring scoped and maintainable styles.
* **AxesMenu Subcomponent**: The `AxesMenu` component, which will be analyzed later, is imported and included as part of the diagnostics section.
* **WebSocket Communication**: The `sendMessage` and `unsubscribeFromMessages` functions are imported from the `Socket` module for communication with the backend.

### User Activity Tracking with useEffect

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const imHere = ["diagnostics_section"];
  sendMessage(imHere);

  return () => {
    unsubscribeFromMessages();
  };
}, []);
```
{% endtab %}
{% endtabs %}

* **Tracking Section Access**:
  * When the component mounts, the `useEffect` hook triggers the `sendMessage` function with the array `["diagnostics_section"]`, which is sent to the backend. This message informs the system that the diagnostics section is currently being accessed.
* **Cleanup on Unmount**:
  * The `return` function within `useEffect` is used to clean up any ongoing message subscriptions by calling `unsubscribeFromMessages`. This ensures that no lingering subscriptions or connections are left open when the component is no longer in use, preventing potential memory leaks.

### Rendering the UI

{% tabs %}
{% tab title="JavaScript" %}
```javascript
return (
  <div className={style.diagnostics_section}>
    <div className={style.diagnostics_blur}>
      <h1 className={style.section_title}>DIAGNOSTICS</h1>
      <AxesMenu />
    </div>
  </div>
);
```
{% endtab %}
{% endtabs %}

* **Main Section Container**: The component is wrapped in a `div` with a class of `diagnostics_section`, which applies the necessary styles to the entire diagnostics section.
* **Blur Effect**: Another `div` with the class `diagnostics_blur` is used to create a visual effect that might be intended to draw focus to the content or differentiate this section visually from others.
* **Title**: An `h1` element with the class `section_title` prominently displays the title "DIAGNOSTICS", indicating the current section to the user.
* **AxesMenu Subcomponent**: The `AxesMenu` component is included, which will render the detailed diagnostics information about the exoskeleton's axes.

## Summary

The **Diagnostics** component is a vital part of the application's monitoring and maintenance functionality. By sending a message to the backend each time the section is accessed, the component helps in tracking user interactions, which can be essential for understanding usage patterns and ensuring that the exoskeleton operates within its intended parameters.

{% hint style="info" %}
The next step would be to analyze the <mark style="color:blue;">**AxesMenu**</mark> component in detail, as it plays a central role in providing the specific diagnostics information displayed within this section.
{% endhint %}
