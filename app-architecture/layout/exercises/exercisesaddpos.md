# ExercisesAddPos

**App Architecture: ExercisesAddPos Component**

The `ExercisesAddPos` component allows users to create and manage a sequence of exercise positions for an exoskeleton hand device. It features **drag-and-drop reordering**, **dynamic speed and pause settings**, and **position management** (add, delete, or import). The configuration can be saved to a JSON file via a backend API.

## **Key Features**

1. **Dynamic Position Management**:
   * Add new positions with a default configuration.
   * Reorder positions using drag-and-drop.
   * Delete specific positions.
   * Import the current position of the exoskeleton hand into the list.
2. **Speed and Pause Control**:
   * Set execution speed for each position.
   * Configure pauses between exercise steps.
   * Apply "lock" functionality to synchronize speed/pause values across all positions.
3. **Data Persistence**:
   * Save and update the configured exercises to a back-end server.
   * Prompt users to save changes before navigating away from the page.
4. **UI Feedback**:
   * Use sliders for intuitive speed/pause adjustments.
   * Display modal confirmation dialogs when unsaved changes exist.
   * Provide buttons for importing, executing, and managing individual positions.

## **Component Overview**

### **Imports**

* **React and Hooks**: Used for state management and lifecycle handling.
* **CSS Modules**: `ExercisesAddPos.module.css` provides scoped styling.
* **React Router**: Navigation and URL parameter handling.
* **Material-UI**: Components like `Slider` and `Dialog` for enhanced UI functionality.
* **Drag-and-Drop**: `@hello-pangea/dnd` enables reordering positions.
* **Socket Communication**: Functions for sending and receiving messages to/from the exoskeleton system.

### **State Variables**

<table data-header-hidden data-full-width="false"><thead><tr><th></th><th></th><th></th></tr></thead><tbody><tr><td><strong>State Variable</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>data</code></td><td><code>object</code></td><td>Stores data received from the socket, including current positions of the exoskeleton.</td></tr><tr><td><code>positions</code></td><td><code>array</code></td><td>Tracks the list of positions with axis values.</td></tr><tr><td><code>speedValues</code></td><td><code>array</code></td><td>Stores speed percentages for each position.</td></tr><tr><td><code>pauseValues</code></td><td><code>array</code></td><td>Stores pause durations (in seconds) for each position.</td></tr><tr><td><code>divOrder</code></td><td><code>array</code></td><td>Manages the display order of positions.</td></tr><tr><td><code>isSpeedLockActivated</code></td><td><code>boolean</code></td><td>Determines if the speed lock is active.</td></tr><tr><td><code>isPauseLockActivated</code></td><td><code>boolean</code></td><td>Determines if the pause lock is active.</td></tr><tr><td><code>isDataChanged</code></td><td><code>boolean</code></td><td>Tracks if unsaved changes exist.</td></tr><tr><td><code>isDataSaved</code></td><td><code>boolean</code></td><td>Tracks if the current state is saved to the server.</td></tr><tr><td><code>modalOpen</code></td><td><code>boolean</code></td><td>Controls the visibility of the confirmation modal.</td></tr><tr><td><code>saveCount</code></td><td><code>number</code></td><td>Tracks whether data is being saved for the first time or updated.</td></tr></tbody></table>

### **Core Functions**

#### **Position Management**

* **`handleAddDiv`**: Adds a new position with default values.
* **`handleDeleteDiv`**: Removes a position and adjusts the state accordingly.
* **`onDragEnd`**: Handles drag-and-drop events to reorder positions.

#### **Speed and Pause Control**

* **`handleSpeedChange`**: Adjusts the speed value for a specific position. If the speed lock is active, all positions synchronize.
* **`handlePauseChange`**: Similar to `handleSpeedChange`, but for pause durations.
* **`handleSpeedLockToggle` / `handlePauseLockToggle`**: Toggles the lock state for speed and pause.

#### **Socket Communication**

* **`startPosExercises`**: Captures the current exoskeleton position into the specified exercise slot.
* **`handleGoClick`**: Sends the axis and speed configurations for a specific position to the exoskeleton for execution.

#### **Data Persistence**

* **`saveExercisesData`**: Sends the position data to the server for saving or updating based on `saveCount`.
* **`handleOpenModal`**: Prompts the user to save changes before navigating away.
* **`firstFetchData`**: Initializes the component state with the actual positions received from the socket.

### **Key Functionalities**

#### **1. Drag-and-Drop Interface**

* Utilizes the `@hello-pangea/dnd` library for reordering exercise positions.
* Reordering adjusts both the position data and associated speed and pause values.

**Code Example:**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<DragDropContext onDragEnd={onDragEnd}>
  <Droppable droppableId="positions">
    {(provided) => (
      <div {...provided.droppableProps} ref={provided.innerRef}>
        {positions.map((position, index) => (
          <Draggable key={index} draggableId={index.toString()} index={index}>
            {(provided) => (
              <div {...provided.draggableProps} ref={provided.innerRef}>
                <div {...provided.dragHandleProps}>Drag Handle</div>
                {/* Position Details */}
              </div>
            )}
          </Draggable>
        ))}
        {provided.placeholder}
      </div>
    )}
  </Droppable>
</DragDropContext>
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### **2. Dynamic Position Management**

* **Adding Positions:** Adds a new position to the sequence, initializing it with default values.
* **Deleting Positions:** Removes a position and updates associated data arrays.
* **Reordering:** Adjusts the sequence order dynamically.

**Code Example: Adding a Position**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleAddDiv = () => {
  setPositions([...positions, { axis1: "", axis2: "", axis3: "", axis4: "", axis5: "" }]);
  setDivOrder([...divOrder, divCount]);
  setSpeedValues([...speedValues, 5]);
  setPauseValues([...pauseValues, 0]);
  setDivCount(divCount + 1);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### **3. Speed and Pause Settings**

* Users can configure speed (0–100%) and pause (0–5 seconds) for each position.
* **Lock Feature:** Applies changes to all positions simultaneously if locked.

**Code Example: Handling Speed Change**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleSpeedChange = (index, newValue) => {
  const updatedSpeedValues = [...speedValues];
  updatedSpeedValues[index] = newValue;

  if (isSpeedLockActivated) {
    updatedSpeedValues.fill(newValue); // Apply to all positions
  }

  setSpeedValues(updatedSpeedValues);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### **4. Position Import and Go**

* **Import:** Captures the current position of the exoskeleton hand and updates the specified position.
* **Go:** Sends the position and speed settings to the backend for execution.

**Code Example: Sending Position Data**

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleGoClick = (index) => {
  const selectedPosition = positions[index];
  const axisValues = {
    4: 0,
    50: selectedPosition.axis1,
    51: selectedPosition.axis2,
    // other axes...
  };

  const axisSpeed = {
    85: speedValues[index],
    86: speedValues[index],
    // other axes...
  };

  sendMessage(axisSpeed);
  sendMessage(axisValues);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### **5. Saving Data**

* Positions, speeds, and pauses are sent to the backend via a POST request.
* Distinguishes between the first save (`save_exercises`) and updates (`update_pos`).

**Code Example: Saving Data**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const saveExercisesData = () => {
  const endpoint = saveCount === 0 ? "save_exercises" : "update_pos";

  const exercises = positions.map((position, index) => ({
    name: exerciseNameURL,
    rep: exerciseRepURL,
    order_pos: divOrder[index],
    axis_1: position.axis1 || 0,
    axis_2: position.axis2 || 0,
    axis_3: position.axis3 || 0,
    speed: speedValues[index] || 0,
    pause: pauseValues[index] || 0,
  }));

  fetch(`http://${ipAddress}:3001/${endpoint}`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(exercises),
  }).then(() => {
    setSaveCount((prev) => prev + 1);
  });
};
```
{% endtab %}
{% endtabs %}

## Summary

This component provides a robust and interactive way to configure exoskeleton exercise sequences, emphasizing flexibility, usability, and integration with the backend API.
