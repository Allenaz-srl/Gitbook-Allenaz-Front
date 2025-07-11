# ErrorWarning

**App Architecture: ErrorWarning Component**

The ErrorWarning component in the AllyArm application is designed to alert users to issues that might impact the safe and effective operation of the exoskeleton device ("Ally"). This component ensures that users are promptly informed about critical errors and warnings, facilitating immediate responses to maintain safety and device functionality.

## **Overview**

The ErrorWarning component is responsible for:

* **Error Notifications**: Displaying alerts to inform users about issues detected in the system.
* **Warning Prompts**: Providing additional details or recommendations for user action in case of warnings.
* **Real-Time Monitoring**: Integrating with backend services to receive and display real-time data regarding system status.
* **UI Interactions**: Supporting user interaction to acknowledge alerts or take guided action as needed.

## **Component Breakdown**

Here’s a detailed breakdown of the code and its functionality:

### **State Management**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [errorMessages, setErrorMessages] = useState([]);
const [isErrorModalOpen, setIsErrorModalOpen] = useState(false);
const [currentError, setCurrentError] = useState(null);
const [warningMessages, setWarningMessages] = useState([]);
const [isWarningModalOpen, setIsWarningModalOpen] = useState(false);
const [currentWarning, setCurrentWarning] = useState(null);
```
{% endtab %}
{% endtabs %}

**State Variables**:

* **errorMessages**: An array that stores detected error messages.
* **isErrorModalOpen**: A boolean that controls the visibility of the error modal.
* **currentError**: The specific error currently being displayed.
* **warningMessages**: An array that stores warning notifications.
* **isWarningModalOpen**: A boolean that determines if the warning modal is visible.
* **currentWarning**: The specific warning currently shown to the user.

### **WebSocket Integration**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleErrorMessage = (message) => {
    setErrorMessages((prev) => [...prev, message]);
    setCurrentError(message);
    setIsErrorModalOpen(true);
  };

  const handleWarningMessage = (message) => {
    setWarningMessages((prev) => [...prev, message]);
    setCurrentWarning(message);
    setIsWarningModalOpen(true);
  };

  subscribeToErrorMessages(handleErrorMessage);
  subscribeToWarningMessages(handleWarningMessage);

  return () => {
    unsubscribeFromErrorMessages(handleErrorMessage);
    unsubscribeFromWarningMessages(handleWarningMessage);
  };
}, []);
```
{% endtab %}
{% endtabs %}

**WebSocket Subscription**: The component listens for incoming error and warning messages from the backend via WebSocket. When a new message arrives, the relevant state is updated, and the respective modal is opened to inform the user.

### **Modal Management**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleCloseErrorModal = () => {
  setIsErrorModalOpen(false);
  setCurrentError(null);
};

const handleCloseWarningModal = () => {
  setIsWarningModalOpen(false);
  setCurrentWarning(null);
};
```
{% endtab %}
{% endtabs %}

**Modal Logic**: Functions that manage the closing of modals and reset the current error or warning being displayed.

### **UI Rendering**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
return (
  <div className={style.errorWarningContainer}>
    <Dialog
      open={isErrorModalOpen}
      onClose={handleCloseErrorModal}
      sx={{ '& .MuiPaper-root': { background: '#f44336' } }}
    >
      <DialogTitle>Error Detected</DialogTitle>
      <DialogContent>
        <DialogContentText>{currentError}</DialogContentText>
      </DialogContent>
      <DialogActions>
        <Button onClick={handleCloseErrorModal} color="primary">Acknowledge</Button>
      </DialogActions>
    </Dialog>

    <Dialog
      open={isWarningModalOpen}
      onClose={handleCloseWarningModal}
      sx={{ '& .MuiPaper-root': { background: '#ff9800' } }}
    >
      <DialogTitle>Warning</DialogTitle>
      <DialogContent>
        <DialogContentText>{currentWarning}</DialogContentText>
      </DialogContent>
      <DialogActions>
        <Button onClick={handleCloseWarningModal} color="primary">Understood</Button>
      </DialogActions>
    </Dialog>
  </div>
);
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Button Rendering**: Modals include buttons for user acknowledgment of the messages.

**Modal Rendering**: Two separate dialog components handle error and warning notifications. Custom styling ensures visual differentiation between error (red background) and warning (orange background) modals.

## **Summary**

The ErrorWarning component is an essential feature of the AllyArm application, ensuring users are informed about potential issues in real-time. By using state management and WebSocket integration, this component provides an interactive and safety-focused experience, guiding users to respond effectively to alerts. Its structured UI and straightforward acknowledgment process reinforce the reliability and user safety of the AllyArm system.
