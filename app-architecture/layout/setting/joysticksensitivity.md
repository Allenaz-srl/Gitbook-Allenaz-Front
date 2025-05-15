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

# JoystickSensitivity

## **App Architecture**

The `JoystickSensitivity` component allows users to adjust the sensitivity of various joystick axes using sliders. The component provides synchronization options for simultaneous adjustments across axes and ensures these settings can be saved or reset to default values.

***

## **Key Features**

* **Individual and Synchronized Sensitivity Adjustment**: Users can modify the sensitivity of each joystick axis separately or synchronize all axes for uniform adjustment.
* **Lock and Unlock Functionality**: Locks or unlocks the sliders for simultaneous sensitivity changes.
* **Data Persistence**: Saves the sensitivity settings to the backend and allows resetting to default values.
* **Dynamic Slider Updates**: Reflects real-time changes based on WebSocket messages.

***

## **Component Responsibilities**

* Display five sliders, each controlling the sensitivity of a joystick axis.
* Allow locking/unlocking of sliders to sync adjustments.
* Persist changes by sending data to the backend.
* Provide "Save" and "Default" buttons for committing or resetting values.
* Handle real-time updates from WebSocket messages.

***

## **Component Breakdown**

The component initializes state variables to manage the sensitivity of five joystick axes, lock state, and other configurations:

```javascript
javascriptCopia codiceconst [joyValue1, setJoyValue1] = useState(5);
const [joyValue2, setJoyValue2] = useState(5);
const [joyValue3, setJoyValue3] = useState(5);
const [joyValue4, setJoyValue4] = useState(5);
const [joyValue5, setJoyValue5] = useState(5);
const [isLocked, setIsLocked] = useState(false);
const [isLockerPressed, setIsLockerPressed] = useState(false);
const [data, setData] = useState({});
const [sliderChanged, setSliderChanged] = useState(false);
```

### **Fetching and Updating Data**

The component subscribes to WebSocket messages to retrieve the current joystick sensitivity settings and updates the state accordingly:

```javascript
javascriptCopia codiceuseEffect(() => {
  const handleDataUpdate = (data) => {
    const { joystickSensData } = data;
    setData(joystickSensData);
  };

  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);

useEffect(() => {
  if (data.lockJoySens !== undefined && !isLockerPressed) {
    setIsLocked(data.lockJoySens);
  }
  if (data["80"] && !sliderChanged) setJoyValue1(data["80"]);
  if (data["81"] && !sliderChanged) setJoyValue2(data["81"]);
  if (data["82"] && !sliderChanged) setJoyValue3(data["82"]);
  if (data["83"] && !sliderChanged) setJoyValue4(data["83"]);
  if (data["84"] && !sliderChanged) setJoyValue5(data["84"]);
}, [data, sliderChanged]);
```

***

### **Handling Slider Changes**

Each slider has a dedicated handler function that updates its value. If the sliders are locked, changes to one slider propagate to all others:

```javascript
javascriptCopia codiceconst handleChangeJoy1 = (event, newValue) => {
  setJoyValue1(newValue);
  if (isLocked) {
    setJoyValue2(newValue);
    setJoyValue3(newValue);
    setJoyValue4(newValue);
    setJoyValue5(newValue);
  }
  setSliderChanged(true);
};
```

(Repeated similarly for other sliders.)

***

### **Lock and Sync Functionality**

The `handleSync` function toggles the lock state. When locking, it synchronizes all sliders to the same value:

```javascript
javascriptCopia codiceconst handleSync = () => {
  setIsLocked(!isLocked);
  setIsLockerPressed(true);
  if (!isLocked) {
    setJoyValue2(joyValue1);
    setJoyValue3(joyValue1);
    setJoyValue4(joyValue1);
    setJoyValue5(joyValue1);
  }
  setSliderChanged(true);
};
```

***

### **Saving and Resetting**

The `handleSave` function sends the current slider values to the backend, while `handleDefault` resets all values to default:

```javascript
javascriptCopia codiceconst handleSave = () => {
  const dataToSave = {
    80: joyValue1,
    81: joyValue2,
    82: joyValue3,
    83: joyValue4,
    84: joyValue5,
    lockJoySens: isLocked && sliderChanged,
  };
  sendMessage(dataToSave);
};

const handleDefault = () => {
  setJoyValue1(5);
  setJoyValue2(5);
  setJoyValue3(5);
  setJoyValue4(5);
  setJoyValue5(5);
  setSliderChanged(true);
};
```

***

### **UI Components**

1.  **Lock/Unlock Button**: Toggles the lock state of sliders.

    {% code overflow="wrap" %}
    ```javascript
    javascriptCopia codice<button className={style.joy_slider_locker_btn} onClick={handleSync}>
      {isLocked ? <BsFillLockFill size={26} color="red" /> : <BsFillUnlockFill size={26} color="green" />}
    </button>
    ```
    {% endcode %}
2.  **Sliders**: Adjustable components for each joystick axis.

    ```javascript
    javascriptCopia codice<Slider
      className={style.joy_slider_item}
      value={joyValue1}
      onChange={handleChangeJoy1}
      aria-label="Joy 1"
      step={null}
      min={0}
      max={10}
      marks={marks}
    />
    ```
3.  **Buttons**: Save and reset buttons.

    {% code overflow="wrap" %}
    ```javascript
    javascriptCopia codice<button className={style.joy_slider_btn_save} onClick={handleSave}>
      {t("common.0.save")}
    </button>
    <button className={style.joy_slider_btn_default} onClick={handleDefault}>
      {t("common.0.default")}
    </button>
    ```
    {% endcode %}

***

{% hint style="info" %}
This component provides precise control over joystick sensitivity while ensuring ease of use through synchronization and reset options.
{% endhint %}
