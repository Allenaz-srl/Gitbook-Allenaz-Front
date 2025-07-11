# AxisSpeed

**App Architecture: AxisSpeed Component**

The `AxisSpeed` component is responsible for setting the speed for each axis individually. It provides sliders for adjusting values, synchronization functionality, and saving/resetting configurations. Below is a detailed breakdown of its features and functionalities.

## **Key Features**

**Component Responsibilities**

1. Display sliders for adjusting speed for each axis (Axis 1 to Axis 5).
2. Synchronize all sliders to the same value when the "lock" feature is enabled.
3. Save and reset speed configurations.
4. Dynamically update values based on incoming data through WebSocket communication.

## **Component Breakdown**

### Initialization

The component uses several state variables to manage the axis values, locking state, and updates from WebSocket messages.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [axisValue1, setAxisValue1] = useState(10);
const [axisValue2, setAxisValue2] = useState(10);
const [axisValue3, setAxisValue3] = useState(10);
const [axisValue4, setAxisValue4] = useState(10);
const [axisValue5, setAxisValue5] = useState(10);
const [isLocked, setIsLocked] = useState(false);
const [isLockerPressed, setIsLockerPressed] = useState(false);
const [data, setData] = useState({});
const [sliderChanged, setSliderChanged] = useState(false);
```
{% endtab %}
{% endtabs %}

* **axisValueX:** Stores the speed value for each axis.
* **isLocked:** Indicates whether the sliders are synchronized.
* **data:** Stores incoming WebSocket data.
* **sliderChanged:** Tracks whether a slider has been manually adjusted.

### WebSocket Integration

The component subscribes to WebSocket messages to fetch initial values and updates for axis speeds.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (data) => {
    const { axisSpeedBaseData } = data;
    setData(axisSpeedBaseData);
  };

  subscribeToMessages(handleDataUpdate);
  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```
{% endtab %}
{% endtabs %}

* **`subscribeToMessages(handleDataUpdate):`** Listens for data updates.
* **`unsubscribeFromMessages(handleDataUpdate):`** Removes the listener on component unmount.

Incoming data is stored in `data` and used to set initial slider values.

### Sloder Interaction

The sliders allow the user to adjust individual axis speeds. If the lock is enabled, changing one slider updates all others.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleChangeAxis1 = (event, newValue) => {
  setAxisValue1(newValue);
  if (isLocked) {
    setAxisValue2(newValue);
    setAxisValue3(newValue);
    setAxisValue4(newValue);
    setAxisValue5(newValue);
  }
  setSliderChanged(true);
};
```
{% endtab %}
{% endtabs %}

Similar handlers are implemented for each axis (`handleChangeAxis2`, `handleChangeAxis3`, etc.).

### Synchronization and Save

The lock button toggles synchronization between all sliders. The "Save" button sends the current configuration via WebSocket.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleSync = () => {
  setIsLocked(!isLocked);
  setIsLockerPressed(true);
  if (!isLocked) {
    setAxisValue2(axisValue1);
    setAxisValue3(axisValue1);
    setAxisValue4(axisValue1);
    setAxisValue5(axisValue1);
  }
  setSliderChanged(true);
};

const handleSave = () => {
  const dataToSave = {
    70: axisValue1,
    71: axisValue2,
    72: axisValue3,
    73: axisValue4,
    74: axisValue5,
    lockSpeed: isLocked && sliderChanged,
  };
  sendMessage(dataToSave);
};
```
{% endtab %}
{% endtabs %}

* **handleSync:** Locks/unlocks all sliders for synchronized adjustments.
* **handleSave:** Sends the current axis configurations to the server.

### Default Reset

The "Default" button resets all sliders to their default value (10%)

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleDefault = () => {
  setAxisValue1(10);
  setAxisValue2(10);
  setAxisValue3(10);
  setAxisValue4(10);
  setAxisValue5(10);
  setSliderChanged(true);
};
```
{% endtab %}
{% endtabs %}

### UI Components

* **Sliders:** `@mui/material/Slider` components for axis speed adjustments.
* **Lock Button:** Toggles synchronization, represented by a lock/unlock icon.
* **Save/Default Buttons:** For saving and resetting configurations.

{% hint style="info" %}
This component integrates real-time data updates and interactive UI elements to allow precise speed configuration for multiple axes.
{% endhint %}
