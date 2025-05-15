# AllyArm Mobile app

### 📘 Introduzione

#### Panoramica del progetto

Il progetto ha lo scopo di permettere la **connessione semplificata tra uno smartphone e il front dell'esoscheletro**, evitando di dover lavorare manualmente con configurazioni di reti e indirizzi ip.\
L'obiettivo viene raggiunto tramite una connessione Bluetooth Low Energy (BLE), per ricevere l'indirizzo IP attuale del Raspberry e configurare le impostazioni WiFi in modo rapido.

#### Obiettivi

* Ricevere l'indirizzo IP dell'esoscheletro (indirizzo IP dinamico e quindi sempre diverso)
* Configurare le impostazioni WIFI dell'esoscheletro tramite interfaccia semplificata
* Visualizzare l'interfaccia dell'esoscheletro tramite WebBrowser embedded

#### Strumenti

* Flutter per lo sviluppo dell'applicazione mobile
* NodeJS per il server BLE e per configurare il WIFI dell'esoscheletro
