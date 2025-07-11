# JoystickScenarioName

## **App Architecture**

The `JoystickScenarioName` component is part of the joystick scenario management module. Its purpose is to create a new scenario by prompting the user to input a unique name, which is then validated against existing scenarios fetched from the server. Once a valid name is entered, the user can proceed to the next step of configuration.

***

## **Key Features**

* **Scenario Name Input**: Accepts user input for a new scenario name.
* **Validation**: Ensures the name is unique and conforms to specific rules.
* **Navigation**: Provides options to return to the previous page or proceed to the next step.
* **Access to Backend Data**: Fetches existing scenario names from the server to prevent duplication.

***

## **Component Responsibilities**

* Display an input field for the user to enter the new scenario name.
* Validate the entered name to ensure it is unique and meets format requirements.
* Enable or disable the "NEXT" button based on validation.
* Communicate with the backend to fetch existing scenario names.

***

## **Component Breakdown**

### **Initialization**

The component initializes state variables for managing the input field, button state, and scenario names:

```javascript
javascriptCopia codiceconst [scenarioName, setScenarioName] = useState(""); // Current input for the scenario name
const [isNextActive, setIsNextActive] = useState(false); // Button activation state
const [data, setData] = useState([]); // List of existing scenario names
const [ipAddress, setIpAddress] = useState(""); // IP address for backend communication
```

WebSocket subscriptions are used to retrieve the server's IP address dynamically:

```javascript
javascriptCopia codiceuseEffect(() => {
  const handleDataUpdate = (newData) => setIpAddress(newData.ipAddress);

  subscribeToMessages(handleDataUpdate);

  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```

***

### **Fetching and Updating Data**

Once the IP address is set, the component fetches the existing scenario names:

```javascript
javascriptCopia codiceuseEffect(() => {
  if (ipAddress) {
    fetch(`http://${ipAddress}:3001/scenario_names`)
      .then((response) => response.json())
      .then((dataFromServer) => {
        setData(dataFromServer.scenarioNames);
        setIsNextActive(scenarioName !== "" && !dataFromServer.scenarioNames.includes(scenarioName));
      })
      .catch((error) => console.error("Error fetching data:", error));
  }
}, [scenarioName, ipAddress]);
```

***

### **Validating and Updating Input**

The `handleScenarioNameChange` function processes user input, ensuring it adheres to restrictions:

* Allows only alphanumeric characters and spaces.
* Limits the length to 24 characters.
* Activates the "NEXT" button if the input is valid and unique:

```javascript
javascriptCopia codiceconst handleScenarioNameChange = (e) => {
  const value = e.target.value.replace(/[^a-zA-Z0-9\s]/g, "").substr(0, 24);
  setScenarioName(value);
  setIsNextActive(value !== "" && !data.includes(value));
};
```

***

### **UI Components**

**Back Button**: Allows navigation to the previous screen.

```javascript
javascriptCopia codice<div className={style.joystick_scenario_name_add_back}>
  <Link to="/joystick_scenario">
    <button className={style.joystick_scenario_name_add_back_btn}>
      <BiArrowBack /> BACK
    </button>
  </Link>
</div>
```

**Input Field**: Captures the scenario name:

```javascript
javascriptCopia codice<div className={style.joystick_scenario_name_add_rename_wrapper}>
  <p className={style.joystick_scenario_name_add_title}>Name of scenario</p>
  <input
    className={style.joystick_scenario_name_add_input_name}
    type="text"
    placeholder="Enter a name"
    value={scenarioName}
    onChange={handleScenarioNameChange}
  />
</div>
```

**Next Button**: Enabled only when a valid name is entered:

```javascript
javascriptCopia codice<div className={style.joystick_scenario_name_add_btn_save_box}>
  <Link to={`/joystick_mode?name=${scenarioName}`}>
    <button
      className={`${style.joystick_scenario_name_add_btn_save} ${isNextActive ? "" : style.disabled}`}
      disabled={!isNextActive}
    >
      NEXT
    </button>
  </Link>
</div>
```

***

{% hint style="info" %}
This component ensures user input is validated before proceeding, providing a seamless and secure flow for scenario creation.
{% endhint %}
