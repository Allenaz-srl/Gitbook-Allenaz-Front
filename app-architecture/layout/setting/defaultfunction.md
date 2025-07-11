# DefaultFunction

**App Architecture: DefaultFunctions Component**

The `DefaultFunctions` component allows users to manage specific preset positions of the exo-arm and assign custom names to the buttons located in the **HOME** section. These buttons are designed for saving and recalling unique arm positions, enabling the user to return the exo-arm to a saved position with a single button click.

## **Key Features**

**Component Responsibilities**

1. Save the current exo-arm position to a specific HOME button.
2. Rename the HOME buttons with user-defined labels.
3. Display the current exo-arm position in degrees for each axis.
4. Dynamically update button names and arm positions based on WebSocket data.

## **Component Breakdown**

### Initialization

The component initializes state variables for managing button names, exo-arm positions, and user input.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [data, setData] = useState({});
const [defaultValues, setDefaultValues] = useState(["", "", "", "", "", ""]);
const [homeBtnValues, setHomeBtnValues] = useState({});
const { t } = useTranslation();
```
{% endtab %}
{% endtabs %}

* **data:** Stores real-time position data of the exo-arm.
* **defaultValues:** Holds user input for renaming buttons.
* **homeBtnValues:** Contains the saved button names from WebSocket data.
* **t:** Translation function for multi-language support.

### Fetching and Updating Data

The component listens for updates via WebSocket to keep the arm positions and button names in sync.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setData(newData);
    const { homeBtn } = newData;
    setHomeBtnValues(homeBtn || {});
  };

  subscribeToMessages(handleDataUpdate);
  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```
{% endtab %}
{% endtabs %}

* **subscribeToMessages:** Subscribes to WebSocket updates for real-time data.
* **unsubscribeFromMessages:** Cleans up the subscription on unmount.

### Saving Exo-Arm Positions

Users can save the current arm position to a HOME button. The saved data includes the position of each axis.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleDefaultPosBtn = (buttonNumber) => {
  const currentData = data.actualPosition || [];
  const btnData = currentData.reduce((acc, value, index) => {
    const address = 21 + buttonNumber * 5 + index;
    return { ...acc, [address]: value };
  }, {});
  sendMessage(btnData);
};
```
{% endtab %}
{% endtabs %}

* **currentData:** Retrieves the current position of the exo-arm.
* **btnData:** Formats the data to be sent for saving.

### Renaming Buttons

The buttons can be renamed by enteringa custom label and pressing the "Rename" button.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleRename = (index) => {
  const inputValue = defaultValues[index];
  const updatedData = { [`default_${index + 1}`]: inputValue };
  sendMessage(updatedData);
};

const handleInputChange = (index, value) => {
  const newDefaultValues = [...defaultValues];
  newDefaultValues[index] = value;
  setDefaultValues(newDefaultValues);
};
```
{% endtab %}
{% endtabs %}

* **handleInputChange:** Updates the input value in the state.
* **handleRename:** Sends the new name to the server for updating the button label.

### UI Components

1. **Position Display: List the current position of the exo-arm in degrees for each axis.**
2. **Button Management:**
   * **Input Field:** Allows users to enter a new button name.
   * **Rename Button**: Sends the new name to the server.
   * **Position Button**: Saves the current arm position to the corresponding button.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<div className={style.default_setting_item}>
  <div className={style.default_actual_name_wrapper}>
    <p>{t("setting.0.btn")} 1 - </p>
    <span>{homeBtnValues.default_1}</span>
  </div>
  <div className={style.default_setting_box}>
    <input
      type="text"
      placeholder="Enter a new name"
      onChange={(e) => handleInputChange(0, e.target.value)}
    />
    <button onClick={() => handleRename(0)}>{t("setting.0.rename")}</button>
    <button onClick={() => handleDefaultPosBtn(0)}>{t("setting.0.pos")}</button>
  </div>
</div>
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
This component combines dynamic updates, user input handling, and server communication to allow flexible management of exo-arm positions and button labels.
{% endhint %}
