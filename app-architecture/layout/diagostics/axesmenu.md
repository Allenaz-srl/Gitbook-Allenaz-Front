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

# AxesMenu

#### Detailed Breakdown: `AxesMenu` Component

The **AxesMenu** component is designed to present a detailed and interactive view of various diagnostic data related to the exoskeleton's axes. The user can select different axes or other categories like "Master" and "Cartesian Data" to view specific data. This component receives its data from the backend, which it processes and displays in a user-friendly format.

## Key Functionalities

* **State Management**:
  * **Data State**: The component maintains the data it receives from the backend.
  * **Selection State**: Tracks the user's selection to determine which data to display.
* **Data Mapping**:
  * Converts numeric codes received from the backend into human-readable strings for the driver and master working modes and states.
* **Rendering and Interaction**:
  * The component dynamically renders buttons for each axis, and the user can click to view detailed information about that specific axis or other categories.
* **Backend Communication**:
  * The component subscribes to real-time data updates from the backend and handles unsubscriptions when the component unmounts.

## Detailed Component Walkthrough

### Importing Dependencies

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React, { useState, useEffect } from "react";
import style from "./AxesMenu.module.css";
import { subscribeToMessages, unsubscribeFromMessages } from "../../Socket";
```
{% endtab %}
{% endtabs %}

* **React**: The component uses `useState` for managing local state and `useEffect` for handling side effects like subscribing to data updates.
* **CSS Module**: `style` object is imported to apply scoped styles defined in `AxesMenu.module.css`.
* **Socket Functions**: `subscribeToMessages` and `unsubscribeFromMessages` are used to manage real-time communication with the backend.

### State Initialization and Data Mapping

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const [data, setData] = useState({});
const driverWorkingMode = {
  0: "Driver Off",
  1: "Position int profiler",
  2: "Pos. int profiler wait for start",
  3: "Speed Percent",
  4: "Speed ABS",
  5: "Soft Stop",
  6: "Hard Stop",
  7: "Stand Still (Waiting State)",
  8: "Stand Still (Auto State)",
  9: "Complaint Trasparent 1",
};

const driverState = { 0: "Not Connected", 1: "Setup Needed", 2: "Waiting", 3: "Auto", 4: "Error" };

const masterWorkingMode = {
  0: "OFF",
  1: "STAND STILL",
  2: "TEST",
  3: "JOG",
  4: "Position Targer",
  5: "Pos. Target wait for start",
  6: "STOP",
  7: "Reset Error",
};

const masterState = { 0: "StartUp", 1: "Waiting", 2: "Auto", 3: "Error", 4: "Transition to Auto", 5: "Transition to Waiting" };
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Data State (`data`)**: Holds the data received from the backend.
* **Mode and State Mappings**: These objects map numeric codes (received from the backend) to descriptive strings that are user-friendly.

### Helper Functions

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const getDriverWorkingMode = (index) => {
  return driverWorkingMode[index] || "";
};

const getDriverState = (index) => {
  return driverState[index] || "";
};

const getMasterWorkingMode = (index) => {
  return masterWorkingMode[index] || "";
};

const getMasterState = (index) => {
  return masterState[index] || "";
};
```
{% endtab %}
{% endtabs %}

* These helper functions take an index (numeric code) and return the corresponding string from the predefined mappings. If the index does not exist in the map, an empty string is returned.

### Data Subscription with `useEffect`

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setData(newData);
  };

  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages();
  };
}, []);
```
{% endtab %}
{% endtabs %}

* **Data Handling**: When new data is received from the backend, it is passed to the `handleDataUpdate` function, which updates the local `data` state.
* **Subscription Management**: Subscribes to data messages when the component mounts and cleans up by unsubscribing when it unmounts.

### User Interaction Handling

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [selectedOption, setSelectedOption] = useState(null);

const handleOptionClick = (option) => {
  setSelectedOption(option);
};
```
{% endtab %}
{% endtabs %}

* **Selected Option State**: Tracks which axis or category the user has selected.
* **Click Handler**: Updates the `selectedOption` state when a user clicks on an axis or category button.

### Rendering the UI

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.axes_menu_section}>
    {data.axisNumberTest &&
      data.axisNumberTest > 0 &&
      Array.from({ length: data.axisNumberTest }, (_, index) => (
        <button key={`axis-btn-${index + 1}`} className={style.axes_menu_btn} onClick={() => handleOptionClick(`Option ${index + 1}`)}>
          Axes {index + 1}
        </button>
      ))}
    <button className={style.axes_menu_btn} onClick={() => handleOptionClick("Option Master")}>
      Master
    </button>
    <button className={style.axes_menu_btn} onClick={() => handleOptionClick("Option Cart Data")}>
      Cart Data
    </button>
    <button className={style.axes_menu_btn} onClick={() => handleOptionClick("Option About")}>
      About
    </button>
    {selectedOption && selectedOption.startsWith("Option") ? (
      <div>
        {Array.from({ length: data.axisNumberTest }, (_, index) => {
          const dataIndex = index + 1;
          return (
            selectedOption === `Option ${dataIndex}` && (
              <div key={`option-${dataIndex}`}>
                <ul className={style.data_list}>
                  <h2 className={style.data_title}>{`Axis ${dataIndex}`}</h2>
                  <h3 className={style.data_title_sub}>Live data</h3>
                  <li className={style.data_list_item}>
                    <p className={style.data_item}>
                      Parity:{" "}
                      <span className={style.data_item_value} id={`parity${index}`}>
                        {data.parity && data.parity[index]}
                      </span>
                    </p>
                  </li>
                  ...
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Dynamic Button Rendering**: The number of buttons corresponding to the axes is dynamically generated based on the `axisNumberTest` value from the `data` object.
* **Option-Specific Data Rendering**: The selected option dictates which data is displayed. This data is rendered within a structured list for easy viewing.

### Specific Option Data Rendering

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
{selectedOption === "Option Master" && (
  <ul className={style.data_list}>
    <h2 className={style.data_title}>MASTER</h2>
    <h3 className={style.data_title_sub}>Live data</h3>
    <li className={style.data_list_item}>
      <p className={style.data_item}>
        Parity:{" "}
        <span className={style.data_item_value} id="parity5">
          {data.parity && data.parity.length > 0 ? data.parity[data.parity.length - 1] : ""}
        </span>
      </p>
    </li>
    ...
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Master Data**: If the user selects "Option Master," the component displays specific information related to the master axis.
* **Conditional Rendering**: Only the selected option’s data is rendered, keeping the UI clean and focused.

## Summary

The **AxesMenu** component is a sophisticated tool for visualizing and interacting with the exoskeleton's diagnostic data. By mapping raw numeric data into understandable terms and allowing users to choose what specific data to view, it provides a clear and user-friendly interface for monitoring the system’s status.
