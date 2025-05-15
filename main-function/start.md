---
description: Start App
---

# Start

The AllyArm application is designed to start automatically upon system boot, ensuring a seamless user experience from the moment the device is powered on.

The application is initiated through a startup script located within the application directory. This script, named `start.sh`, is responsible for navigating to the application's root directory and launching the application using npm.

Here’s the content of the `start.sh` script:

{% tabs %}
{% tab title="Node.js" %}
```javascript
#!/bin/bash
cd /home/raspberry/Allenaz_Client/ && sudo npm run start
```
{% endtab %}
{% endtabs %}

### How It Works:

1. **Directory Navigation**: The script first navigates to the directory where the AllyArm application is installed (`/home/raspberry/Allenaz_Client/`).
2. **Application Start**: Once in the correct directory, the script executes the command `sudo npm run start`, which initiates the application.

### Automatic Startup:

The system is configured to run the `start.sh` script automatically when the device is powered on. This setup ensures that AllyArm starts without any manual intervention, providing users with immediate access to the application's features as soon as the system is ready.

This approach is particularly useful in environments where consistent uptime and accessibility of the application are crucial.
