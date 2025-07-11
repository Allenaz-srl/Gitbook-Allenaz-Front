# JoystickModeEdit

## &#x20;**App Architecture**

The `JoystickModeEdit` component is part of the Joystick Mode section. It allows users to edit or modify joystick operation modes, including setting specific configurations for various axes (e.g., X, Y, Z). The component fetches and saves scenario data, offering an interactive UI for customization.

## **Key Features**

* Displays a list of joystick modes with visual representations.
* Supports adding, editing, and deleting joystick modes.
* Allows users to invert axis configurations and select custom images for each axis.
* Communicates with the backend for fetching and saving scenario data.

## **Component Responsibilities**

* **Data Fetching**: Retrieve and transform scenario data from the backend.
* **Mode Management**: Add, update, and delete joystick modes.
* **Interactive UI**: Provide controls for axis inversion and image selection.
* **Backend Communication**: Save updated joystick configurations to the backend.

***

## **Component Breakdown**

### **Initialization**

State variables manage the component's data and UI states:

```javascript
javascriptCopia codiceconst [isModalOpen, setIsModalOpen] = useState(false);
const [selectedImage, setSelectedImage] = useState(null);
const [isOtherImageSelectedMap, setIsOtherImageSelectedMap] = useState({});
const [modalCheckboxChecked, setModalCheckboxChecked] = useState(false);
const [modeName, setModeName] = useState("");
const [modeCart, setModeCart] = useState("");
const [ipAddress, setIpAddress] = useState("");
```

The `modes` state holds the list of joystick modes:

```javascript
javascriptCopia codiceconst [modes, setModes] = useState([
  {
    name: "MODE 1",
    images: { ...defaultImages },
    inversion: { ...defaultInversion },
  },
]);
```

Predefined images and inversion defaults:

```javascript
javascriptCopia codiceconst rom = { /* image paths */ };
const defaultImages = { /* default image mapping */ };
const defaultInversion = { /* default inversion states */ };
```

***

### **Fetching and Updating Data**

The component subscribes to WebSocket messages to update the `ipAddress` and fetches scenario data:

```javascript
javascriptCopia codiceuseEffect(() => {
  const handleDataUpdate = (newData) => setIpAddress(newData.ipAddress);

  subscribeToMessages(handleDataUpdate);
  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```

Fetching scenario data from the backend:

```javascript
javascriptCopia codiceconst getScenarioData = async () => {
  try {
    const response = await fetch(`http://${ipAddress}:3001/get_scenario_mode`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ scenarioNameURL }),
    });
    const data = await response.json();
    const transformedData = transformDataFromResponse(data);
    setModes(transformedData);
  } catch (error) {
    console.error(error);
  }
};
```

Transforming backend data for the component:

```javascript
javascriptCopia codiceconst transformDataFromResponse = (data) => {
  return data.slice(1).map((item) => ({
    name: item.name,
    inversion: { ...item.inversion },
    images: transformImages(item),
  }));
};
```

***

### **Saving/Updating Data**

Saving scenario configurations to the backend:

```javascript
javascriptCopia codiceconst sendScenarioData = () => {
  const formattedData = [
    { scenario: scenarioNameURL },
    ...modes.map((mode) => ({ ...mode, inversion: { ...mode.inversion } })),
  ];
  fetch(`http://${ipAddress}:3001/save_scenario_mode`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(formattedData),
  }).catch(console.error);
};
```

***

### **Renaming Features**

The component uses mode names (`MODE 1`, `MODE 2`, etc.) and allows the addition of new modes:

{% code overflow="wrap" %}
```javascript
javascriptCopia codiceconst handleAddMode = () => {
  const newMode = `MODE ${modes.length + 1}`;
  setModes([...modes, { name: newMode, images: { ...defaultImages }, inversion: { ...defaultInversion } }]);
};
```
{% endcode %}

***

### **UI Components**

**Modal for Editing Configurations**: The modal lets users select images and toggle inversion for specific axes:

{% code overflow="wrap" %}
```javascript
javascriptCopia codice<Dialog open={isModalOpen} onClose={handleModalNoClick}>
  <DialogTitle>Confirmation</DialogTitle>
  <DialogContent>
    <img src={rom.cart_x} onClick={() => handleImageSelect(rom.cart_x)} />
    <Checkbox checked={modalCheckboxChecked} onChange={() => handleCheckboxChange(modeName, modeCart)} />
  </DialogContent>
  <DialogActions>
    <Button onClick={handleModalSave}>Save</Button>
  </DialogActions>
</Dialog>
```
{% endcode %}

**Joystick Modes List**: Displays the modes with visual and textual information:

```javascript
javascriptCopia codice<ul>
  {modes.map((mode, index) => (
    <li key={index}>
      <img src={mode.images.x} alt="Cart X" />
      <button onClick={() => handleDeleteMode(index)}>Delete</button>
    </li>
  ))}
</ul>
```

***

{% hint style="info" %}
By following this structured documentation, you can accurately describe each aspect of the component, its purpose, and its implementation within your app.
{% endhint %}
