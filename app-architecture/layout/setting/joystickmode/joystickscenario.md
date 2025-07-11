# JoystickScenario

## **App Architecture**

The `JoystickScenario` component manages joystick operation scenarios, providing users with a list of pre-configured modes. It allows for adding, deleting, and selecting scenarios while communicating with the backend for data persistence and updates.

## **Key Features**

* **Scenario Management**: Add, edit, delete, and select joystick scenarios.
* **Current and Selected Scenarios**: Displays the active and user-selected scenarios.
* **Access Control**: Restricts edit/delete actions based on the user's access level.
* **Server Communication**: Fetches scenario data and synchronizes updates with the backend.

## **Component Responsibilities**

* **Data Handling**: Load, display, and manage the list of scenarios.
* **UI Interaction**: Highlight selected scenarios, handle modal confirmations, and provide buttons for operations.
* **Action Execution**: Start a scenario, delete a scenario, or navigate to editing or adding new scenarios.
* **Access Control**: Ensure only authorized users can perform specific actions.

***

## **Component Breakdown**

### **Initialization**

State variables manage the UI and scenario data:

{% code overflow="wrap" %}
```javascript
javascriptCopia codiceconst [data, setData] = useState([]); // List of scenarios
const [isActualScenario, setIsActualScenario] = useState([]); // Current scenario
const [selectedDivIndex, setSelectedDivIndex] = useState(""); // Index of selected scenario
const [selectedExercise, setSelectedExercise] = useState(""); // Selected scenario name
const [updateActualScenario, setUpdateActualScenario] = useState(""); // Updates current scenario
const [exerciseToDelete, setExerciseToDelete] = useState(""); // Scenario marked for deletion
const [isModalOpen, setIsModalOpen] = useState(false); // Modal visibility
const [ipAddress, setIpAddress] = useState(""); // Backend IP address
```
{% endcode %}

The component subscribes to WebSocket messages for IP updates:

```javascript
javascriptCopia codiceuseEffect(() => {
  const handleDataUpdate = (newData) => setIpAddress(newData.ipAddress);
  subscribeToMessages(handleDataUpdate);
  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```

***

### **Fetching and Updating Data**

The `loadScenariosName` function retrieves scenarios and updates the component state:

```javascript
javascriptCopia codiceconst loadScenariosName = async () => {
  try {
    const response = await fetch(`http://${ipAddress}:3001/scenario_names`);
    if (!response.ok) throw new Error("Failed to fetch scenarios names.");
    const data = await response.json();
    setData(data.scenarioNames);
    setIsActualScenario(data.lastScenario);
    console.log("Scenarios Names:", data);
  } catch (error) {
    console.error(error);
  }
};
```

Fetching begins after the IP address is known:

```javascript
javascriptCopia codiceuseEffect(() => {
  if (ipAddress) loadScenariosName();
}, [ipAddress]);
```

***

### **Saving/Updating Data**

The `handleScenarioPlay` function sends the selected scenario to the backend:

```javascript
javascriptCopia codiceconst handleScenarioPlay = () => {
  fetch(`http://${ipAddress}:3001/play_scenario_mode`, {
    method: "POST",
    headers: { "Content-Type": "text/plain" },
    body: selectedExercise,
  })
    .then((response) => {
      if (!response.ok) throw new Error("Failed to send data to the server");
      console.log("Scenario started successfully");
    })
    .catch(console.error);
  setUpdateActualScenario(selectedExercise);
};
```

Deleting a scenario involves sending a request and refreshing the list:

```javascript
javascriptCopia codiceconst handleDeleteScenario = (item) => {
  fetch(`http://${ipAddress}:3001/delete_scenario`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ scenario: item }),
  })
    .then((response) => {
      if (!response.ok) throw new Error("Failed to delete scenario");
      return response.json();
    })
    .then(() => loadScenariosName())
    .catch(console.error);
};
```

***

### **Modal Handling**

The modal confirms deletion actions:

```javascript
javascriptCopia codice<Dialog open={isModalOpen} onClose={handleModalNoClick}>
  <DialogTitle>Confirmation</DialogTitle>
  <DialogContent>
    <DialogContentText>
      Delete this <span>{exerciseToDelete}</span> scenario?
    </DialogContentText>
  </DialogContent>
  <DialogActions>
    <Button onClick={handleModalYesClick}>Yes</Button>
    <Button onClick={handleModalNoClick}>No</Button>
  </DialogActions>
</Dialog>
```

***

### **UI Components**

**Scenario List**: Displays scenarios with options for editing or deleting:

{% code overflow="wrap" %}
```javascript
javascriptCopia codice<div className={style.joystick_scenario_list}>
  {data.map((item, index) => (
    <div
      key={index}
      className={`${style.joystick_scenario_list_box} ${selectedDivIndex === index ? style.selected_div : ""}`}
      onClick={() => handleExerciseNameClick(item, index)}
    >
      <p>{item}</p>
      <div>
        <Link to={`/joystick_mode_edit?name=${item}`}>
          <button>
            <AiFillEdit size={24} fill={accessLevel >= 15 ? "rgb(255, 251, 0)" : "gray"} />
          </button>
        </Link>
        <button onClick={() => handleDeleteExercisesButton(item)} disabled={accessLevel < 15}>
          <RiDeleteBin6Line size={24} fill={accessLevel < 15 ? "gray" : "rgb(226, 29, 29)"} />
        </button>
      </div>
    </div>
  ))}
</div>
```
{% endcode %}

**Add Button**: Navigates to the `JoystickName` component for creating a new scenario:

{% code overflow="wrap" %}
```javascript
javascriptCopia codice<Link to="/joystick_name">
  <button>
    <AiOutlinePlusCircle style={{ width: "40px", height: "40px", fill: accessLevel >= 15 ? "#00ff00" : "gray" }} />
  </button>
</Link>
```
{% endcode %}

***

{% hint style="info" %}
This documentation provides a clear understanding of the `JoystickScenario` component's architecture, features, and code.
{% endhint %}
