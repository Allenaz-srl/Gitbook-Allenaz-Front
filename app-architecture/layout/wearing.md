# Wearing

#### App Architecture: Wearing Component

The **Wearing** component is the initial page users encounter after logging in to the AllyArm application. This component plays a crucial role in preparing the exoskeleton (referred to as "Ally") for operation by allowing the user to command Ally to assume specific positions, namely the "Wearing" and "Relax" positions. The component also facilitates the transition to the main dashboard (Home) once Ally is ready.

## Overview

The Wearing component is primarily responsible for:

1. **Initiating Commands**: It allows users to send commands to Ally to either assume the "Wearing" position (ready for the user to put on) or "Relax" (return to a neutral position).
2. **Interactive Buttons**: There are four buttons that control Ally's actions:
   * **"Wearing Position"**: Commands Ally to move into a position where the user can wear the device.
   * **"Let's Start!"**: Activated after confirming that Ally is worn correctly. This button initiates the primary operational mode.
   * **"Relax?"**: Allows the user to command Ally to return to a neutral position.
   * **"Free Position"**: Final command that confirms Ally has returned to a rest state.
3. **Modal Dialogs**: The component uses modal dialogs for confirmations and warnings. For instance, when the user clicks "Let's Start!" or "Relax?", a confirmation dialog appears to ensure that the user is ready and that Ally is in the correct state.

## Component Breakdown

Here's a detailed breakdown of the code and its functionality:

### State Management

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [buttonPresses, setButtonPresses] = useState({});
const [newData, setNewData] = useState({});
const [dressedButtonActive, setDressedButtonActive] = useState(true);
const [restButtonActive, setRestButtonActive] = useState(false);
const [startCheckWearing, setStartCheckWearing] = useState(false);
const [startCheckRest, setStartCheckRest] = useState(false);
const [wearingButtonClicked, setWearingButtonClicked] = useState(false);
const [restButtonClicked, setRestButtonClicked] = useState(false);
const [isModalOpen, setIsModalOpen] = useState(false);
const [isSecondModalOpen, setIsSecondModalOpen] = useState(false);
```
{% endtab %}
{% endtabs %}

**State Variables**:

* **buttonPresses**: Tracks which button was pressed, sending the corresponding command to the server.
* **newData**: Holds incoming data from the backend (via WebSocket).
* **dressedButtonActive** & **restButtonActive**: Control the activation state of the "Let's Start!" and "Free Position" buttons, respectively.
* **startCheckWearing** & **startCheckRest**: Flags to initiate checks on Ally's status after sending commands.
* **wearingButtonClicked** & **restButtonClicked**: Track if the respective position commands have been triggered.
* **isModalOpen** & **isSecondModalOpen**: Control the visibility of the confirmation and warning modals.

### WebSocket Integration

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (data) => {
    setNewData(data);
  };
  subscribeToMessages(handleDataUpdate);

  return () => {
    unsubscribeFromMessages(handleDataUpdate);
  };
}, []);
```
{% endtab %}
{% endtabs %}

* **WebSocket Subscription**: The component subscribes to messages from the backend to receive updates on Ally's state. The `handleDataUpdate` function updates the local state (`newData`) with incoming data.

### Command Handling

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleButtonClick = (buttonId) => {
  if (buttonId === "wearing_position") {
    setTimeout(() => {
      setWearingButtonClicked(true);
      setStartCheckWearing(true);
    }, 1500);
    setRestButtonActive(false);
  }
  if (buttonId === "rest_position") {
    setTimeout(() => {
      setRestButtonClicked(true);
      setStartCheckRest(true);
    }, 500);
  }

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

* **Command Execution**: When a button is clicked, a specific command (`buttonId`) is sent to the backend via WebSocket. For instance, clicking the "Wearing Position" button sends a command to move Ally into the wearing position and starts checking if the motion completes successfully.

### Modal Management

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleStartButton = () => {
  setIsModalOpen(true);
};

const handleModalNoClick = () => {
  setIsModalOpen(false);
};

const handleOpenSecondModal = () => {
  setIsSecondModalOpen(true);
};

const handleSecondModalYesClick = () => {
  setIsSecondModalOpen(false);
  setRestButtonActive(true);
  handleButtonClick("rest_ROM_position");
};

const handleSecondModalNoClick = () => {
  setIsSecondModalOpen(false);
};
```
{% endtab %}
{% endtabs %}

* **Modal Logic**: These functions manage the opening and closing of modal dialogs that confirm user actions. For example, before Ally moves to the "Relax" position, the user is warned to remove their arm from the device.

### UI Rendering

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.wearing_section}>
    <div className={style.wearing_blur}>
      <Dialog
        open={isModalOpen}
        onClose={handleModalNoClick}
        sx={{
          "& .MuiPaper-root": {
            background: "#4d494d",
          },
        }}
      >
        <DialogTitle sx={{ color: "rgb(0, 165, 255)" }}>Confirmation</DialogTitle>
        <DialogContent>
          <DialogContentText sx={{ color: "white" }}>Did you dressed Ally correctly?</DialogContentText>
        </DialogContent>
        <DialogActions>
          <Link to="/home">
            <Button onClick={() => handleButtonClick("wearing_ROM_position")} color="primary">Yes</Button>
          </Link>
          <Button onClick={handleModalNoClick} color="primary">
            No
          </Button>
        </DialogActions>
      </Dialog>
      
      <Dialog
        open={isSecondModalOpen}
        onClose={handleSecondModalNoClick}
        sx={{
          "& .MuiPaper-root": {
            background: "#4d494d",
          },
        }}
      >
        <DialogTitle
          sx={{
            color: "white",
            background: "repeating-linear-gradient(-45deg, orange, orange 10px, black 10px, black 20px)",
            marginBottom: "10px",
            display: "flex",
            justifyContent: "center",
            alignItems: "center",
          }}
        >
          <DialogContentText
            sx={{
              color: "black",
              background: "orange",
              fontWeight: "600",
              padding: "0 10px",
            }}
          >
            &#128680;WARNING!&#128680;
          </DialogContentText>
        </DialogTitle>
        <DialogContent>
          <DialogContentText sx={{ color: "white" }}>
            Before sending Ally into "free position", you need to remove your arm from the device!
          </DialogContentText>
          <DialogContentText sx={{ color: "white" }}>Have you already removed your arm from Ally?</DialogContentText>
        </DialogContent>
        <DialogActions>
          <Button
            onClick={handleSecondModalYesClick}
            sx={{
              color: "green",
              fontWeight: "600",
            }}
          >
            Yes
          </Button>
          <Button
            onClick={handleSecondModalNoClick}
            sx={{
              color: "red",
              fontWeight: "600",
            }}
          >
            No
          </Button>
        </DialogActions>
      </Dialog>
      
      <div className={style.wearing_btn_container}>
        <button className={style.wearing_btn} onClick={() => handleButtonClick("wearing_position")}>
          {t("wearing.0.wearing")}
        </button>
        <button
          className={`${style.wearing_btn_lets_start} ${dressedButtonActive ? "" : style.disabled}`}
          disabled={!dressedButtonActive}
          onClick={handleStartButton}
        >
          LET'S START!
        </button>
        <button className={style.wearing_btn} onClick={handleOpenSecondModal}>
          RELAX?&#128564;
        </button>
        <button
          className={`${style.wearing_btn_lets_start} ${restButtonActive ? "" : style.disabled}`}
          disabled={!restButtonActive}
          type="submit"
          onClick={() => handleButtonClick("rest_position")}
        >
          {t("wearing.0.free")}
        </button>
      </div>
    </div>
  </div>
);
```
{% endcode %}
{% endtab %}
{% endtabs %}

* **Button Rendering**: The buttons are rendered within a container, with conditional styling and disabling based on the exoskeleton's current state. The `t` function is used for internationalization, allowing the button text to change based on the user's language setting.
* **Modal Rendering**: The component includes two modal dialogs. One for confirming that the user has correctly worn Ally before proceeding, and another as a warning to ensure safety before transitioning Ally to a relaxed state.

## Summary

The Wearing component is a critical part of the AllyArm application’s workflow, ensuring that users can correctly and safely prepare the exoskeleton before starting any operations. It handles state management, user interactions through buttons, and displays modals for user confirmation, ensuring that the user is guided through the process with appropriate checks and safety measures.
