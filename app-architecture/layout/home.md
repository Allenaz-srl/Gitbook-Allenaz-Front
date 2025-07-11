# Home

#### App Architecture: Home Component

The **Home** component is the next stage in the user flow after the exoskeleton (AllyArm) has been correctly worn and initialized via the Wearing component. This component serves as the main control interface, allowing the user to start and stop the exoskeleton, as well as trigger predefined movements based on the user’s custom settings.

## Overview

The Home component has the following primary functionalities:

1. **Start and Stop Operations**: Two main buttons, "Start" and "Stop," control the activation and deactivation of the exoskeleton. These are crucial for initiating and halting operations safely.
2. **Customizable Movement Buttons**: The component features several buttons that users can customize. These buttons correspond to specific movements or positions that the exoskeleton can assume. For instance, a button might be configured to move the arm to a position near a table, or any other position the user has defined in the settings.
3. **Dynamic Button Labels**: The labels of these customizable buttons are dynamically updated based on the user’s settings. This allows for a highly personalized experience where each button’s purpose and label can be adapted to the user's needs.

## Component Breakdown

Here's a detailed breakdown of the Home component's code and functionality:

### State Management

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [buttonPresses, setButtonPresses] = useState({});
const [homeBtnValues, setHomeBtnValues] = useState({});
```
{% endtab %}
{% endtabs %}

* **buttonPresses**: This state tracks which button was pressed and sends the corresponding command to the server.
* **homeBtnValues**: This state holds the labels for the customizable buttons, which are fetched from the backend based on the user's settings.

### WebSocket Integration

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (data) => {
    const { homeBtn } = data;
    setHomeBtnValues(homeBtn);
  };

  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);
```
{% endtab %}
{% endtabs %}

* **Data Subscription**: The component subscribes to incoming messages from the backend, which provide updates on the labels for the customizable buttons (`homeBtn`). This ensures that any changes made in the settings are reflected in real time on the Home page.

### Button Handling

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

* **Command Execution**: When a button is pressed, the corresponding command (`buttonId`) is sent to the backend via WebSocket. This could be to start, stop, or move Ally to a predefined position.

### UI Rendering

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.home_section}>
    <div className={style.home_blur}>
      <button type="" className={style.home_btn_start} onClick={() => handleButton("start")}>
        START
      </button>
      <div className={style.home_btn_container}>
        <button className={style.home_btn} onClick={() => handleButton("default1")}>
          {homeBtnValues.default_1}
        </button>
        <button className={style.home_btn} onClick={() => handleButton("default2")}>
          {homeBtnValues.default_2}
        </button>
        <button className={style.home_btn} onClick={() => handleButton("default3")}>
          {homeBtnValues.default_3}
        </button>
        <button className={style.home_btn} onClick={() => handleButton("default4")}>
          {homeBtnValues.default_4}
        </button>
        <button className={style.home_btn} onClick={() => handleButton("default5")}>
          {homeBtnValues.default_5}
        </button>
        <button className={style.home_btn} onClick={() => handleButton("default6")}>
          {homeBtnValues.default_6}
        </button>
      </div>
      <button type="" className={style.home_btn_stop} onClick={() => handleButton("stop")}>
        STOP
      </button>
    </div>
  </div>
);
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Start Button**: This button sends the "start" command to initiate the exoskeleton's operations.
* **Custom Buttons**: These buttons correspond to different predefined movements or positions, with their labels dynamically set according to user preferences (`homeBtnValues.default_1` to `homeBtnValues.default_6`).
* **Stop Button**: This button sends the "stop" command to halt the exoskeleton's operations.

## Summary

The Home component is central to the operation of the AllyArm exoskeleton. It provides the user with essential controls to start and stop the device and allows for the execution of custom movements that the user has configured. The integration with the backend via WebSocket ensures that the interface remains dynamic and responsive to the user’s settings, offering a personalized and flexible control environment for the exoskeleton.
