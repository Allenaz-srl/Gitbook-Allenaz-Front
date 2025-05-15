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

# ExercisesAdd

**App Architecture: ExercisesAdd Component**

The **ExercisesAdd** component allows users to create a new exercise by specifying its name and the number of repetitions. It validates input, ensures user-friendly restrictions, and provides navigation to the next step where further configuration occurs.

## **Key Features**

1. **Exercise Creation**:
   * Accepts an exercise name (with character restrictions).
   * Specifies the number of repetitions (numeric range from 1 to 100).
2. **Validation**:
   * Prevents invalid characters in the name.
   * Ensures both fields are filled before proceeding.
3. **Navigation**:
   * Includes a "Back" button to return to the main **Exercises** page.
   * "Next" button becomes active only when inputs are valid.

## **Component Breakdown**

Here’s a detailed breakdown of the code and its functionality:

### **State Management**

The component uses `useState` to manage input fields and button activation:

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const [exerciseName, setExerciseName] = useState(""); // Stores the entered name of the exercise.
const [exerciseRep, setExerciseRep] = useState(""); // Stores the entered number of repetitions.
const [isNextActive, setIsNextActive] = useState(false); // Controls whether the "Next" button is enabled.
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Input Handling

#### Exercise Name Input

The `handleExerciseNameChange` function ensures:

* Only alphanumeric characters and spaces are allowed.
* Maximum length is 24 characters.
* Updates `isNextActive` based on validation of both fields.

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
const handleExerciseNameChange = (e) => {
  const value = e.target.value.replace(/[^a-zA-Z0-9\s]/g, "").substr(0, 24);
  setExerciseName(value);
  setIsNextActive(value !== "" && exerciseRep !== "");
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

#### **Repetition Input**

The `handleExerciseRepChange` function ensures:

* Only numeric input is accepted.
* Updates `isNextActive` based on validation of both fields.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const handleExerciseRepChange = (e) => {
  const value = e.target.value;
  setExerciseRep(value);
  setIsNextActive(exerciseName !== "" && value !== "");
};
```
{% endtab %}
{% endtabs %}

### Navigation

The component offers two navigation options:

1. **Back**:\
   A button linked to the main **Exercises** page.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<Link to="/exercises">
  <button className={style.exercises_add_back_btn}>
    <BiArrowBack /> BACK
  </button>
</Link>
```
{% endtab %}
{% endtabs %}

2. **Next**:\
   A button leading to the **ExercisesAddPos** page with query parameters for the exercise name and repetitions. The button is disabled until both fields are valid.

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<Link to={`/exercises_pos_add?name=${exerciseName}&repetition=${exerciseRep}`}>
  <button className={`${style.exercises_add_btn_save} ${isNextActive ? "" : style.disabled}`} disabled={!isNextActive}>
    NEXT
  </button>
</Link>
```
{% endcode %}
{% endtab %}
{% endtabs %}

### UI Components

**Input Fields**

1. **Name Input**:\
   Accepts the exercise name with a placeholder and validation.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<input
  className={style.exercise_add_input_name}
  type="text"
  placeholder="Enter a name"
  value={exerciseName}
  onChange={handleExerciseNameChange}
/>
```
{% endtab %}
{% endtabs %}

2. **Repetition Input**:\
   Accepts the number of repetitions with numeric restrictions.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<input
  className={style.exercise_add_input_value}
  type="number"
  min="1"
  max="100"
  placeholder="Repeat"
  value={exerciseRep}
  onChange={handleExerciseRepChange}
/>
```
{% endtab %}
{% endtabs %}

#### **Buttons**

1. **Back Button**:\
   Provides a visually styled button with an arrow icon.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<button className={style.exercises_add_back_btn}>
  <BiArrowBack /> BACK
</button>
```
{% endtab %}
{% endtabs %}

2. **Next Button**:\
   A conditional button that redirects to the next page when enabled.

{% tabs %}
{% tab title="JavaScript" %}
{% code overflow="wrap" %}
```javascript
<button className={`${style.exercises_add_btn_save} ${isNextActive ? "" : style.disabled}`} disabled={!isNextActive}>
  NEXT
</button>
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Styling

All styles are imported from `ExercisesAdd.module.css`, with notable classes including:

* `exercises_add_section`: Overall layout styling.
* `exercises_add_blur`: Background blur effect.
* `exercises_add_back_btn`: Back button styling.
* `disabled`: Disabled state for buttons.

## Summary

The **ExercisesAdd** component simplifies the process of adding exercises by providing clear input validation and an intuitive interface. It ensures that user entries are properly formatted before proceeding, laying the groundwork for additional configuration in the **ExercisesAddPos** component.
