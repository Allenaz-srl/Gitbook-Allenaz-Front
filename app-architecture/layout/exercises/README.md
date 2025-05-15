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

# Exercises

**App Architecture: Exercises Component**

The **Exercises** component serves as a central interface for managing and interacting with exercise routines in your application. It allows users to view, select, edit, delete, and play exercises. Additionally, it integrates real-time updates and backend communication for dynamic content management.

## Key Features

The ErrorWarning component is responsible for:

* **Exercise Management**:
  * Display a list of available exercises.
  * Allows users to select an exercise from the list.
* **Exercise Actions**:
  * Add a new exercise via a navigation button.
  * Edit or delete existing exercises through action buttons.
* **Real-Time Updates**:
  * Integrates with WebSocket for real-time updates from the backend.
* **UI Modals**:
  * Provides confirmation dialogs for delete actions.
* **State Persistence**:
  * Saves and restores the currently selected exercise across sessions.

## **Component Breakdown**

Here’s a detailed breakdown of the code and its functionality:

### **State Management**

The component uses `useState` to manage its state variables and `useEffect` for side effects. Key state variables include:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const [exerciseName, setExerciseName] = useState([]); // Stores list of exercises.
const [dBData, setDBData] = useState([]); // Stores backend data.
const [isModalOpen, setIsModalOpen] = useState(false); // Controls the visibility of the delete modal.
const [exerciseToDelete, setExerciseToDelete] = useState(""); // Tracks the exercise marked for deletion.
const [selectedExercise, setSelectedExercise] = useState({ name: "", rep: 0 }); // Currently selected exercise.
const [isPaused, setIsPaused] = useState(true); // Tracks play/pause state.
const [selectedDivIndex, setSelectedDivIndex] = useState(""); // Index of the selected exercise in the UI.
const [ipAddress, setIpAddress] = useState(""); // Stores backend IP address for API calls.
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Backend Integration

#### WebSocket Subscriptions

The component subscribes to backend messages to receive updates. This ensures real-time data synchronization:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
useEffect(() => {
  const handleDataUpdate = (newData) => {
    setDBData(newData);
    setIpAddress(newData.ipAddress);
  };

  subscribeToMessages(handleDataUpdate);
  return () => {
    unsubscribeFromMessages();
  };
}, []);
```
{% endtab %}
{% endtabs %}

#### Fetching Exercises

Exercise data is fetched dynamically from the backend using the provided `ipAddress`:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const loadExerciseNames = async () => {
  try {
    const response = await fetch(`http://${ipAddress}:3001/exercises_names`);
    if (!response.ok) throw new Error("Failed to fetch exercises names.");
    const data = await response.json();
    setExerciseName(data);
  } catch (error) {
    console.error(error);
  }
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

### User Interaction

#### Play Selected Exercise

The `handlePlayActualExercise` function triggers playback of the selected exercise by sending its details to the backend:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handlePlayActualExercise = () => {
  fetch(`http://${ipAddress}:3001/exercises_data`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name: selectedExercise.name }),
  })
    .then((response) => response.json())
    .then((data) => {
      dataChanger(data);
      localStorage.setItem("selectedExercise", JSON.stringify(selectedExercise));
    })
    .catch((error) => console.error(error));
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### Delete Exercises

Users can delete an exercise via the confirmation modal. The `handleDeleteExercisesButton` function opens the modal, and the final deletion is handled by `handleDeleteExercise`:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleDeleteExercise = (name) => {
  fetch(`http://${ipAddress}:3001/delete_exercise`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ exerciseName: name }),
  })
    .then((response) => response.json())
    .then(loadExerciseNames)
    .catch((error) => console.error(error));
};
```
{% endtab %}
{% endtabs %}

### UI Components

#### Exercise List

Exercises are displayed as a scrollable list with individual cards:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<div className={style.exercise_scroll_list}>
  {exerciseName.map((exercise, index) => (
    <div
      className={`${style.exercise_main_box} ${selectedDivIndex === index ? style.selected_div : ""}`}
      key={index}
      onClick={() => handleExerciseNameClick(exercise, index)}
    >
      <p className={style.exercises_name_input}>{exercise.name}</p>
      {/* Edit/Delete Buttons */}
    </div>
  ))}
</div>
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### Modal Dialog

The delete confirmation modal is styled and conditionally rendered:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<Dialog
  open={isModalOpen}
  onClose={handleModalNoClick}
  sx={{ "& .MuiPaper-root": { background: "#4d494d" } }}
>
  <DialogTitle sx={{ color: "rgb(0, 165, 255)" }}>Confirmation</DialogTitle>
  <DialogContent>
    <DialogContentText sx={{ color: "white" }}>
      Delete this <span className={style.exercises_delete_name}>{exerciseToDelete}</span> exercise?
    </DialogContentText>
  </DialogContent>
  <DialogActions>
    <Button onClick={handleModalYesClick} color="primary">Yes</Button>
    <Button onClick={handleModalNoClick} color="primary">No</Button>
  </DialogActions>
</Dialog>
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Summary

The **Exercises** component provides a robust and interactive interface for managing exercises. With features like real-time updates, persistent state, and backend integration, it ensures a seamless user experience. Upcoming components, **ExercisesAdd** and **ExercisesEdit**, extend its functionality for exercise creation and modification.
