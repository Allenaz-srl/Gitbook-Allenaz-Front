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

# Test

App Architecture: AutomaticMode (Test) Component

The AutomaticMode (Test) component is designed for technical maintenance and performance testing of the exoskeleton arm. This section of the application allows technicians or developers to run specific test commands by interacting with a dynamically generated set of buttons. Each button corresponds to a predefined command, which is executed when the button is pressed.

## Overview

The AutomaticMode component is a critical tool for testing the various functions and movements of the exoskeleton. The key features of this component include:

1. **Dynamic Button Rendering**: Buttons are rendered based on the entries in the `testBtn.json` file, which specifies the available commands for testing.
2. **Command Execution**: Each button triggers a specific command that is sent to the backend for execution, facilitating the testing of different functionalities.
3. **Stop Functionality**: A dedicated "STOP" button is available to halt any ongoing tests immediately.

## Component Breakdown

Here’s a detailed breakdown of the AutomaticMode component’s code and functionality:

### Importing Dependencies

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useState } from "react";
import style from "./AutomaticMode.module.css";
import mode from "../../../api/testBtn.json";
import { sendMessage } from "../../Socket";
```
{% endtab %}
{% endtabs %}

* **React and State Management**: React is used for rendering the UI, and `useState` manages the state of button presses.
* **Styling**: The component uses a CSS module (`AutomaticMode.module.css`) for scoped styling.
* **Button Configuration**: The `mode` variable imports the JSON configuration, which includes the list of buttons and their corresponding commands.
* **WebSocket Communication**: The `sendMessage` function is used to send commands to the backend through WebSocket.

### State Management

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [buttonPresses, setButtonPresses] = useState({});
```
{% endtab %}
{% endtabs %}

* **buttonPresses**: This state keeps track of the buttons that have been pressed and the corresponding commands that are sent to the backend.

### Button Click Handling

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleButtonClick = (buttonId) => {
  const buttonPress = { 10: 2, 11: buttonId };

  setButtonPresses((prevButtonPresses) => ({
    ...prevButtonPresses,
    ...buttonPress,
  }));

  sendMessage(buttonPress);
};
```
{% endtab %}
{% endtabs %}

* **handleButtonClick**: This function is called when a test button is clicked. It constructs a command object where `10: 2` might represent a predefined action type, and `11: buttonId` specifies the particular test command associated with the button.
* **Command Dispatch**: The command is then sent to the backend using `sendMessage`.

### Stop Button Handling

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleButtonStop = (buttonId) => {
  const buttonPress = [buttonId];

  setButtonPresses((prevButtonPresses) => ({
    ...prevButtonPresses,
    ...buttonPress,
  }));

  sendMessage(buttonPress);
};
```
{% endtab %}
{% endtabs %}

* **handleButtonStop**: This function handles the "STOP" button, which sends a command to stop any ongoing test or action. This ensures that testing can be halted immediately if necessary.

### Rendering the UI

{% tabs %}
{% tab title="JavaScript" %}
```javascript
return (
  <div className={style.mode_section}>
    <div className={style.mode_blur}>
      <div className={style.mode_btn_container}>
        {mode.map((item) => (
          <button
            className={style.mode_btn}
            key={item.id_button}
            onClick={() => handleButtonClick(item.id_button)}
          >
            {item.description_button}
          </button>
        ))}
      </div>
      <button
        type=""
        className={style.mode_btn_stop}
        onClick={() => handleButtonStop("stop")}
      >
        STOP
      </button>
    </div>
  </div>
);
```
{% endtab %}
{% endtabs %}

* **Dynamic Button Rendering**: The buttons are dynamically generated based on the `testBtn.json` file. Each button displays a description (`description_button`) and triggers its corresponding test command when clicked.
* **STOP Button**: This button is always present and allows the user to stop any ongoing test by sending a "stop" command.

## JSON Configuration File

The `mode.json` file provides the configuration for the buttons rendered in the AutomaticMode component:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
[
  {
    "id_button": 1,
    "description_button": "Ciclo movimenti standard"
  },
  {
    "id_button": 3,
    "description_button": "Ciclo movimenti cartesiani"
  },
  {
    "id_button": 4,
    "description_button": "Driver OFF"
  },
  {
    "id_button": 7,
    "description_button": "Test Mouse 3D Asse 1"
  },
  {
    "id_button": 8,
    "description_button": "Test JOG"
  }
]
```
{% endtab %}
{% endtabs %}

* **id\_button**: A unique identifier for each button, which is used in the `handleButtonClick` function to determine the command to send.
* **description\_button**: A brief description of the action associated with the button, displayed on the UI.

## Summary

The AutomaticMode (Test) component is essential for testing and maintaining the exoskeleton arm. By dynamically rendering buttons based on a JSON configuration, it allows for flexible and extensive testing capabilities. The integration with the backend ensures that each test command is executed correctly, and the STOP button provides a critical safety feature to halt operations immediately when needed.
