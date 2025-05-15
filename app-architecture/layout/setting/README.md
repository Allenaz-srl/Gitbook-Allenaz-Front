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

# Setting

**App Architecture: SettingMenu Component**

The `SettingMenu` component provides a menu interface for various application settings. Its behavior and structure are dependent on the `accessLevel` prop, which determines the visibility of specific menu options. Below is a detailed breakdown of the component.

## Overview

`SettingMenu` is a React functional component responsible for rendering a settings menu with multiple options. Each option links to a specific settings page. The menu dynamically adjusts its available options based on the `accessLevel` provided.

## Props

**accessLevel (number)**

* **Description**: Determines which sections of the settings menu are visible.
* **Expected values**: A numeric value representing the user's access level.
* **Behavior**:
  * **Access Level ≥ 15**: Displays advanced settings such as "Speed Advanced" and "ROM".
  * **Access Level < 15**: Hides these advanced settings.

## Structure

The component consists of:

1. Container Elements:
   * A wrapper div (setting\_menu\_section) for overall layout.
   * A blurred background layer (setting\_menu\_blur) for design aesthetics.
2. Button Container:
   * A div (setting\_menu\_btn\_container) containing navigation butons.
   * Each button is wrapped in a Link component from react-router-dom for routing.
3. Buttons:
   * Rendered based on the menu options defined below.

## Menu Options

Below are the available menu options, their visibility conditions, and routing paths:

| **Button Name** | **Translation Key** | **Route** | **Visibility Condition** |
| --------------- | ------------------- | --------- | ------------------------ |

| Speed Advanced | `setting.0.speed_adv` | `/axisspeed_advance` | `accessLevel >= 15` |
| -------------- | --------------------- | -------------------- | ------------------- |

| Speed | `setting.0.speed` | `/axisspeed` | Always visible |
| ----- | ----------------- | ------------ | -------------- |

| Sensitivity | `setting.0.joy` | `/sensitivity` | Always visible |
| ----------- | --------------- | -------------- | -------------- |

| Joystick Mode | _(hardcoded text)_ | `/joystick_scenario` | Always visible |
| ------------- | ------------------ | -------------------- | -------------- |

| Wearing Position | `setting.0.wearing_pos` | `/wearingposition` | Always visible |
| ---------------- | ----------------------- | ------------------ | -------------- |

| Default Functions | `setting.0.def_functions` | `/default_function` | Always visible |
| ----------------- | ------------------------- | ------------------- | -------------- |

| ROM (Range of Motion) | `setting.0.rom` | `/rom` | `accessLevel >= 15` |
| --------------------- | --------------- | ------ | ------------------- |

| Installation | _(hardcoded text)_ | `/installation` | Always visible |
| ------------ | ------------------ | --------------- | -------------- |

## Internationalization (i18)

* Integration: The component uses the **useTranslation** hook from **react-i18next** for multi-languages support.
* Keys:
  * setting.0.speed\_adv → Axis Speed (Advanced)
  * setting.0.speed → Axis Speed
  * setting.0.joy → Joystick sensitivity
  * setting.0.wearing\_pos → Wearing Position
  * setting.0.def\_functions → Default Functions
  * setting.0.rom → ROM (range of motion)

## Key Considerations

1. Access Control:
   * Ensure accessLevel is provided as a prop; otherwise, advanced sections will not render.
2. Translation Support:
   * Missing keys in the translation files might lead to empty text.
3. Routing:
   * Confirm all routes are correctly defined in the application.
