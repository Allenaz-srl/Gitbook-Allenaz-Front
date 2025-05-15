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

# ExercisesEditPos

**App Architecture: ExercisesEditPos Component**

The `ExercisesEditPos` component is designed to edit the positions of an exercise, allowing users to modify parameters such as axis angles, speed, and pauses. It retrieves exercise data from a server, displays it in a draggable and editable list, and provides functionality for saving changes or adding new positions.

## **Key Features**

1. Data Retrieval and Initialization:
   * Fetches exercise data by **id** (from URL parameters) using the **viewExercisePositions** function.
   * Populates positions, speed, and pause values for editing.
2. Position Management:
   * Add, delete, and reorder positions using a drag-and-drop interface provided by **@hello-pangea/dnd**.
3. Speed and Pause Adjustment:
   * Provides sliders for each position to modify speed (0-100%) and pause (0-5 seconds).
   * Optional locking mechanism to apply the same value across all positions.
4. Real-time Updates:
   * Reflects live position data from a WebSocket subscription.
5. Save and Navigate:
   * Allows users to save updated exercise data to the server.
   * Prompts users with a modal if they try to leave without saving changes.

## **Component Overview**

### Imports

The component utilizes several libraries and dependencies:

* **React**: For managing state and lifecycle.
* **React Router**: Handles navigation and URL parameters.
* **Material UI**: Provides sliders, dialogs, and buttons.
* **Icons**: Visual elements for user interactions.
* **Socket Functions**: Custom functions for WebSocket communication.
* **Drag and Drop**: Enables reordering of positions.

### State Variables

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const [data, setData] = useState({});
const [exerciseNameState, setExerciseNameState] = useState("");
const [positions, setPositions] = useState([
  { name: "", rep: "", axis1: "", axis2: "", axis3: "", axis4: "", axis5: "" }
]);
const [speedValues, setSpeedValues] = useState([]);
const [pauseValues, setPauseValues] = useState([]);
const [isSpeedLockActivated, setIsSpeedLockActivated] = useState(false);
const [isPauseLockActivated, setIsPauseLockActivated] = useState(false);
const [isDataChanged, setIsDataChanged] = useState(true);
const [isDataSaved, setIsDataSaved] = useState(false);
const [modalOpen, setModalOpen] = useState(false);
```
{% endtab %}
{% endtabs %}

### Key Functions

1. Data Fetching: **viewExercisePositions**

Retrieves exercise data from the server, formats it, and updates the state.

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const viewExercisePositions = async () => {
  try {
    const response = await fetch(`http://${ipAddress}:3001/view_positions_exercise`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ name: id }),
    });
    const data = await response.json();
    const formattedData = data.result.map(item => ({
      name: item.name,
      rep: item.rep,
      axis1: item.axis_1.toString(),
      axis2: item.axis_2.toString(),
      // Axis values continue...
    }));
    setPositions(formattedData);
  } catch (error) {
    console.error(error);
  }
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

2. Adding New Positions: **handleAddDiv**

Creates a new position with default values and appends it to the list.

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleAddDiv = () => {
  setPositions([
    ...positions,
    { name: defaultName, rep: defaultRep, axis1: "", axis2: "", axis3: "", axis4: "", axis5: "" }
  ]);
  setIsDataChanged(true);
  setIsDataSaved(false);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

3. Saving Data: **saveExercisesData**

Sends the updated positions to the server.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const saveExercisesData = () => {
  const exercises = positions.map((pos, index) => ({
    name: pos.name,
    rep: pos.rep,
    axis_1: pos.axis1 || 0,
    // Additional axis mappings...
    speed: speedValues[index] || 0,
    pause: pauseValues[index] || 0,
  }));
  fetch(`http://${ipAddress}:3001/update_pos`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(exercises),
  })
    .then(() => setIsDataSaved(true))
    .catch(console.error);
};
```
{% endtab %}
{% endtabs %}

4. Drag-and-Drop Handling: **onDragEnd**

Reorders positions and updates state.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const onDragEnd = (result) => {
  if (!result.destination) return;
  const reordered = Array.from(positions);
  const [moved] = reordered.splice(result.source.index, 1);
  reordered.splice(result.destination.index, 0, moved);
  setPositions(reordered);
};
```
{% endtab %}
{% endtabs %}

### Example Usage

#### Adding a New Position

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<button onClick={handleAddDiv}>Add Position</button>
```
{% endtab %}
{% endtabs %}

#### Saving Changes

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<button onClick={saveExercisesData}>Save</button>
```
{% endtab %}
{% endtabs %}

#### Drag-and-Drop Interface

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<DragDropContext onDragEnd={onDragEnd}>
  <Droppable droppableId="positions">
    {(provided) => (
      <div {...provided.droppableProps} ref={provided.innerRef}>
        {positions.map((pos, index) => (
          <Draggable key={index} draggableId={index.toString()} index={index}>
            {(provided) => (
              <div {...provided.draggableProps} ref={provided.innerRef}>
                <span {...provided.dragHandleProps}>Drag</span>
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
{% endtab %}
{% endtabs %}

## Summary

`ExercisesEditPos` combines real-time updates, intuitive editing, and seamless user interaction. It offers flexibility and efficiency for managing exercise configurations, making it essential for applications requiring dynamic adjustments.
