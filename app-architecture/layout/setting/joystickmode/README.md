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

# JoystickMode

## **Overview**

The **JoystickMode** component allows users to manage joystick modes for a scenario, offering functionality to add, delete, and edit modes. It integrates real-time updates via WebSocket, provides a visual interface for mode configuration, and enables data persistence through a server API.

***

## **Core Functionalities**

### **Real-Time Updates**

* Subscribes to WebSocket updates to receive the server's IP address dynamically.
* Cleans up WebSocket subscriptions on component unmount.

```javascript
javascriptCopia codiceuseEffect(() => {
  const handleDataUpdate = (newData) => {
    setIpAddress(newData.ipAddress);
  };

  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);
```

### **Mode Management**

*   **Adding a Mode**

    * Creates a new mode with default images and inversion settings.
    * Automatically scrolls to the newly added mode.

    {% code overflow="wrap" %}
    ```javascript
    javascriptCopia codiceconst handleAddMode = () => {
      const newMode = `MODE ${modesCount + 1}`;
      setModes([...modes, { name: newMode, images: { ...defaultImages }, inversion: { ...defaultInversion } }]);
      setModesCount(modesCount + 1);
    };
    ```
    {% endcode %}
*   **Deleting a Mode**

    * Removes a mode by its index, with a safeguard to prevent deleting the last mode.

    ```javascript
    javascriptCopia codiceconst handleDeleteMode = (index) => {
      setModes((prevModes) => {
        const newModes = [...prevModes];
        newModes.splice(index, 1);
        return newModes;
      });
      setModesCount(modesCount - 1);
    };
    ```
*   **Editing a Mode**

    * Opens a modal to select an image and set inversion for a specific axis of the mode.
    * Updates the selected mode's configuration on saving.

    {% code overflow="wrap" %}
    ```javascript
    javascriptCopia codiceconst handleModalSave = () => {
      setIsModalOpen(false);
      if (modeName && modeCart) {
        setModes((prevModes) => {
          return prevModes.map((mode) => {
            if (mode.name === modeName) {
              const newImages = { ...mode.images };
              const selectedImageUsed = Object.values(newImages).includes(selectedImage);
              for (const key in newImages) {
                if (key === modeCart) {
                  newImages[key] = selectedImageUsed ? rom.other : selectedImage || newImages[key];
                }
              }
              return { ...mode, images: newImages };
            }
            return mode;
          });
        });
      }
    };
    ```
    {% endcode %}

### **Data Persistence**

* Sends the current scenario and modes configuration to the server via a POST request.

{% code overflow="wrap" %}
```javascript
javascriptCopia codiceconst sendScenarioData = () => {
  const formattedData = [
    { scenario: scenarioNameURL },
    ...modes.map((mode) => {
      const formattedMode = { name: mode.name };
      for (const key in mode.images) {
        formattedMode[key] = Object.keys(rom).find((romKey) => rom[romKey] === mode.images[key]) || mode.images[key];
      }
      formattedMode.inversion = { ...mode.inversion };
      return formattedMode;
    }),
  ];

  fetch(`http://${ipAddress}:3001/save_scenario_mode`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(formattedData),
  })
    .then((response) => {
      if (!response.ok) throw new Error("Error in Joystick modes data sending");
    })
    .catch((error) => console.error(error));
};
```
{% endcode %}

***

## **UI Components**

### **Scenario Title and Navigation**

* Displays the name of the current scenario and provides a "Back" button.

```javascript
javascriptCopia codice<div className={style.joystick_mode_add_pos_back}>
  <Link to="/joystick_name">
    <button className={style.joystick_mode_add_pos_back_btn}>
      <BiArrowBack /> BACK
    </button>
  </Link>
</div>
```

### **Mode List**

* Renders a scrollable list of modes with interactive image selection and inversion toggles.

{% code overflow="wrap" %}
```javascript
javascriptCopia codice{modes.map((mode, index) => (
  <div key={index} className={style.joystick_mode_box} ref={index === modes.length - 1 ? lastModeRef : null}>
    <ul className={style.joystick_mode_list}>
      <li className={style.joystick_mode_list_item_subtittle}>{mode.name}</li>
      <li onClick={() => handleStartButton(mode.name, "x", mode.images.x)}>
        <img src={mode.images.x} alt="Cart X" />
      </li>
    </ul>
  </div>
))}
```
{% endcode %}

### **Image Selection Modal**

* Allows selecting images for joystick axes and toggling inversion.

{% code overflow="wrap" %}
```javascript
javascriptCopia codice<Dialog open={isModalOpen} onClose={handleModalNoClick}>
  <DialogTitle>Confirmation</DialogTitle>
  <DialogContent>
    <DialogContentText>Choose joystick mode:</DialogContentText>
    <div className={style.imageSelection}>
      <img src={rom.cart_x} onClick={() => handleImageSelect(rom.cart_x)} alt="Cart X" />
    </div>
  </DialogContent>
  <DialogActions>
    <Button onClick={handleModalSave}>SAVE</Button>
  </DialogActions>
</Dialog>
```
{% endcode %}

### **Save Button**

* Triggers sending the scenario data to the server.

```javascript
javascriptCopia codice<button onClick={sendScenarioData}>SAVE</button>
```

***

## **Key Variables**

* **modes**: Array of joystick modes with names, images, and inversion states.
* **rom**: Object containing default image paths for different joystick axes.
* **defaultImages**: Predefined set of images for new modes.
* **defaultInversion**: Default inversion states for new modes.
* **scenarioNameURL**: Scenario name retrieved from the URL query parameters.

***

{% hint style="info" %}
This component provides a user-friendly interface for managing joystick modes, leveraging dynamic updates, modular state management, and seamless server integration.
{% endhint %}
