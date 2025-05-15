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

# Switch.jsx

The `Switch.jsx` component is a reusable toggle switch that can be integrated into various parts of the Notification Center or the broader application. It’s designed to be visually intuitive and easy to use, offering a simple mechanism for users to toggle between two states (e.g., on/off, show all/show unread).

## 1. **Core Imports and Dependencies**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React from "react";
import style from "./StyleCenter.module.css";
```
{% endtab %}
{% endtabs %}

* **React**: The `Switch` component is a functional component built using React, making it easy to maintain and integrate with other React-based components.
* **CSS Module**: The component uses a CSS module (`StyleCenter.module.css`) for encapsulated styling, ensuring the switch is consistently styled across different parts of the application without causing style conflicts.

## 2. **Component Structure and Props**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
export default function Switch(props) {
  return (
    <label className={style.switch_container}>
      <input className={style.switch_input} type="checkbox" {...props} />
      <span className={style.switch_slider}></span>
    </label>
  );
}
```
{% endtab %}
{% endtabs %}

* **Props**:
  * The `Switch` component accepts any props passed down to it (`...props`). These props are spread onto the `input` element, allowing the component to be highly flexible and customizable. Common props might include `checked`, `onChange`, or `disabled`.
* **JSX Structure**:
  * The component is composed of a `label` element with the class `switch_container`, which acts as the container for the switch.
  * Inside the `label`, there is an `input` element of type `checkbox` which serves as the actual toggle mechanism.
  * A `span` element with the class `switch_slider` is used to visually represent the switch, creating the sliding effect when the switch is toggled.

## 3. **Interactivity and User Experience**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<input className={style.switch_input} type="checkbox" {...props} />
```
{% endtab %}
{% endtabs %}

* **Interactive Element**: The `input` element, hidden visually but still interactive, is the core of the switch's functionality. The `checked` state and event handlers (such as `onChange`) are controlled through props, enabling the switch to reflect and alter its state based on user interactions.
* **User Experience Considerations**:
  * The switch is designed to be responsive, with smooth transitions to ensure a satisfying user experience.
  * The color change when toggled (`#4caf50` for the checked state) is designed to clearly indicate the switch's state, enhancing usability.
