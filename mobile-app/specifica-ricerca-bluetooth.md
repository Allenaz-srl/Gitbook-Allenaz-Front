# Specifica Ricerca Bluetooth

Durante la ricerca bluetooth l'app Flutter cerca i dispositivi bluetooth raggiungibili. Il server Bluetooth su raspberry definisce il nome del nodo Bluetooth. Tale nome deve seguire questa convenzione:

* Inizia con **AllyArm-**
* prosegue con il numero seriale che arriva dal Master
* finisce con #1\*

Esempio: AllyArm-25003#1\*

L'app visualizza nell'elenco dei dispositivi a cui potersi collegare solamente dispositivi il cui nome soddisfa tali requisiti.&#x20;

Nella visualizzazione del nome elimina #1\*.
