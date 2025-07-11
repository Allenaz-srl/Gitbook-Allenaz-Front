# ExercisesEdit

**App Architecture: ExercisesEdit Component**

The **ExercisesEdit** component allows users to modify existing exercises, including their names, repetitions, and associated positions. It also provides navigation to the **ExercisesEditPos** component for detailed position editing.

## **Key Features**

1. **Edit Exercise Metadata**
   * Change the name of the exercise (with validation).
   * Update the number of repetitions.
2. **View Positions**
   * Display positions associated with the selected exercise.
   * Show detailed speed and pause settings for each position.
3. **Save Changes**
   * Ensures unsaved changes are preserved or prompts the user before leaving the page.
4. **Navigation to Position Editor**
   * Redirect to the **ExercisesEditPos** component for detailed editing of positions.

## **Component Overview**

### **State Variables**

| **State Variable** | **Purpose** |
| ------------------ | ----------- |

| `exerciseName` | Stores the name of the exercise being edited. |
| -------------- | --------------------------------------------- |

| `exerciseRep` | Tracks the number of repetitions for the exercise. |
| ------------- | -------------------------------------------------- |

| `dBData` | Holds the position data fetched from the backend. |
| -------- | ------------------------------------------------- |

| `isActive` | Indicates whether there are unsaved changes. |
| ---------- | -------------------------------------------- |

| `isModalOpen` | Controls the visibility of the unsaved changes confirmation modal. |
| ------------- | ------------------------------------------------------------------ |

| `ipAddress` | Stores the backend IP address for API communication. |
| ----------- | ---------------------------------------------------- |

### Fetching Data

The component fetches exercise details and associated positions upon loading.

#### Load Exercise Metadata

Fetches the name and repetition count:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const loadExerciseData = async () => {
  try {
    const response = await fetch(`http://${ipAddress}:3001/edit_exercise`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ exerciseName: id }),
    });
    const data = await response.json();
    setExerciseRep(data.result[0]);
  } catch (error) {
    console.error(error);
  }
};
```
{% endtab %}
{% endtabs %}

#### Fetch Positions

Retrieves position data for display:

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
    setDBData(data.result);
  } catch (error) {
    console.error(error);
  }
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Editing and Saving

#### Edit Name and Repetitions

Inputs allow users to modify the exercise name and repetition count with validations:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<input
  className={style.exercise_edit_input_name}
  type="text"
  placeholder="Enter a new name"
  value={exerciseName}
  onChange={(e) => {
    setExerciseName(e.target.value.replace(/[^a-zA-Z0-9\s]/g, "").substr(0, 24));
    setIsActive(true);
  }}
/>

<input
  className={style.exercise_edit_input_value}
  type="number"
  min="1"
  max="100"
  value={exerciseRep.rep}
  onChange={(e) => {
    setExerciseRep({ rep: e.target.value });
    setIsActive(true);
  }}
/>
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### Save Changes

Validates uniqueness of the exercise name before updating:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleUpdateExercise = async (id, name, rep) => {
  const response = await fetch(`http://${ipAddress}:3001/exercises_names`);
  const exerciseNames = await response.json();
  if (exerciseNames.includes(name)) {
    console.error("Name already exists.");
    return;
  }

  fetch(`http://${ipAddress}:3001/update_exercise`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ oldName: id, newName: name, rep }),
  }).then(() => navigate(`/exercises_edit/${name}`));
};
```
{% endtab %}
{% endtabs %}

### Handling Unsaved Changes

#### Warning Before Navigation

If there are unsaved changes, a modal prompts the user for confirmation:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleBeforeUnload = (e) => {
  if (isActive) {
    const confirmationMessage = "You have unsaved changes. Are you sure you want to leave?";
    e.returnValue = confirmationMessage;
    return confirmationMessage;
  }
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### Modal Implementation

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<Dialog open={isModalOpen} onClose={handleModalNoClick}>
  <DialogTitle>Confirmation</DialogTitle>
  <DialogContent>
    <DialogContentText>
      Are you sure you want to leave this page without saving modifications?
    </DialogContentText>
  </DialogContent>
  <DialogActions>
    <Button onClick={handleModalYesClick}>Yes</Button>
    <Button onClick={handleModalNoClick}>No</Button>
  </DialogActions>
</Dialog>
```
{% endtab %}
{% endtabs %}

### Viewing and Navigating Positions

#### Display Positions

Each position is displayed with its axis values, speed, adn pause setting:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
{dBData.map((position, index) => (
  <div key={index} className={style.exercises_edit_pos_item}>
    <h3>Position {index + 1}</h3>
    <ul>
      <li>{position.axis_1}</li>
      <li>{position.axis_2}</li>
      <li>{position.axis_3}</li>
      <li>{position.axis_4}</li>
      <li>{position.axis_5}</li>
    </ul>
    <div>
      <span>Speed: {position.speed}%</span>
      <span>Pause: {position.pause} sec</span>
    </div>
  </div>
))}
```
{% endtab %}
{% endtabs %}

#### Edit Positions

A button redirects the user to the ExercisesEditPos component for detailed editing:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<Link to={`/exercises_pos_edit/${id}`}>
  <button>Edit Positions</button>
</Link>
```
{% endtab %}
{% endtabs %}

### Component Layout

* **Header**
  * Back button with unsaved changes handling.
  * Exercise name and repetition inputs.
* **Positions List**
  * Displays all associated positions with details.
* **Edit Positions Button**
  * Navigates to the detailed position editor.
* **Save Button**
  * Disabled unless changes are detected.

## Summary

The **ExercisesEdit** component streamlines the process of editing existing exercises. With features like unsaved changes handling, validation, and navigation to position editing, it ensures a seamless and user-friendly experience. The next step in the workflow is the **ExercisesEditPos** component for detailed position management.

