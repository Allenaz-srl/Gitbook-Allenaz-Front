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

# Layout

#### APP Architecture: Layout Section

The **Layout** section of the AllyArm application is central to the user experience after logging in. It contains various interconnected components that facilitate the operation, configuration, and management of the exoskeleton. These components are grouped under the Layout umbrella because they define the primary structure and navigational flow of the application.

## Overview of the Layout Section

The Layout section is composed of several key components, each with its specific role and functionality:

1. **Wearing**: The initial page that users land on after logging in. It allows users to command the exoskeleton to assume specific positions such as "Wearing" or "Relax". This page acts as a transition point where users can prepare the exoskeleton before moving on to more advanced controls.
2. **Home**: The main control hub of the application. This page provides access to essential controls and operations for starting the exoskeleton's functions. It serves as the central dashboard from which users can navigate to different functionalities.
3. **Navbar**: The navigation bar that provides quick access to different sections of the application. It ensures that users can easily move between different pages like Wearing, Home, Setting, Exercises, ErrorWarning, Diagnostics, and Test. The Navbar is persistent across different views, providing consistent navigation.
4. **Setting**: A configuration page where users can adjust various parameters of the exoskeleton's behavior. This includes setting movement limits, configuring control settings, and other advanced options to tailor the exoskeleton’s functionality to the user’s needs.
5. **Exercises**: A page dedicated to creating and customizing exercises with the exoskeleton. Users can define specific movements and routines, which can then be executed by the exoskeleton, making it highly adaptable to individual training needs.
6. **ErrorWarning**: This component displays detailed information about errors and warnings related to the exoskeleton. It includes a history of past errors and provides insights into their causes and possible solutions, ensuring users can diagnose and address issues efficiently.
7. **Diagnostics**: A technical overview page that shows all relevant information about the exoskeleton. This includes real-time data on its status, performance metrics, and other critical diagnostics that help in monitoring and maintaining the device.
8. **Test**: A specialized page for conducting automated tests on the exoskeleton, particularly in "automaticMode". This component is used to ensure that the exoskeleton functions correctly and can execute predefined routines without manual intervention.

## The Role of the Layout Section

The Layout section is the backbone of the AllyArm application’s user interface. It organizes the various functional components into a coherent and accessible structure, guiding users through the different stages of interaction with the exoskeleton. From initial setup in Wearing to detailed diagnostics and troubleshooting in Diagnostics and ErrorWarning, the Layout section ensures that all necessary tools are available and easy to navigate.

Each component within the Layout section is designed with a specific user journey in mind, starting with basic setup and moving towards more advanced controls and configurations. This structure allows for a seamless user experience, where each component builds on the previous one, offering both simplicity for beginners and depth for advanced users.

As we dive into each component in the following sections, we'll explore how they contribute to the overall functionality and usability of the AllyArm application.
