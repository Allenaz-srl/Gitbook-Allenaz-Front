# Arduino Tester

Per testare l'app è stato creato un tester Arduino che simula il server Bluetooth presente su Raspberry.

Ecco il codice:\


```
#include <ArduinoBLE.h>

BLEService wifiService("180F");
BLECharacteristic wifiDataCharacteristic("2A2A", BLEWrite, 128);
BLECharacteristic ssidListCharacteristic("2A3A", BLERead, 128); // Caratteristica per la lista SSID
BLECharacteristic wifiConnectionStatusCharacteristic("2A4A", BLERead, 5);
BLECharacteristic wifiNameCharacteristic("2A5A", BLERead, 128);

/*Nel codice del server (Arduino), possiamo usare un valore a 3 stati:
0: Connessione in corso
1: Connessione eseguita (successo)
2: Connessione fallita*/

String savedPassword = "";  // Password salvata (simulata)
bool useSavedPassword = false;  // Flag per indicare se utilizzare la password salvata

// Lista finta degli SSID
String ssidList[] = {"HOTSPOT", "WiFi_Network_1", "WiFi_Network_2", "WiFi_Network_3"};
int ssidCount = 4;  // Numero di SSID nella lista
// Lista dei bit per ciascun SSID (1 significa password salvata, 0 significa no password)
bool passwordSaved[] = {true, true, false, true};
IPAddress ip(192, 168, 0, 209);
uint8_t statoConnessioneWifi=2;

void setup() {
  Serial.begin(115200);
  delay(2000);
  Serial.println("START");

  if (!BLE.begin()) {
    Serial.println("Errore nell'inizializzazione del BLE!");
    while (1);
  }
  else{
    Serial.println("Inizializzazione del BLE completata!");
  }
  BLE.setLocalName("AllyArm-25003#*"); // Questo è il nome che il dispositivo avrà durante la scansione

  wifiService.addCharacteristic(wifiDataCharacteristic);
  wifiService.addCharacteristic(ssidListCharacteristic);  // Aggiungi la caratteristica per SSID
  wifiService.addCharacteristic(wifiConnectionStatusCharacteristic);
  wifiService.addCharacteristic(wifiNameCharacteristic);
  BLE.addService(wifiService);

  BLE.advertise();
  Serial.println("In attesa di connessioni...");

// simula l'indirizzo ip corrente
  uint8_t response[5] = {1, ip[0], ip[1], ip[2], ip[3]};
  wifiConnectionStatusCharacteristic.writeValue(response, 5);

// simula il nome del wifi a cui è connesso
  String ssid = "WiFi_Network_1";
  String msg = "#" + String(ssid.length()) + ":" + ssid + "*";

  uint8_t data[msg.length()];
  msg.getBytes(data, msg.length());
  wifiNameCharacteristic.writeValue(data, msg.length());
}

void loop() {
  // Mantenere il BLE attivo
  BLE.poll();  // Gestisce le operazioni in background

  BLEDevice central = BLE.central();
  
  if (central) {
    Serial.print("Connesso a: ");
    Serial.println(central.address());

    // Invia la lista SSID alla connessione
    sendSSIDList();

    while (central.connected()) {
      if (wifiDataCharacteristic.written()) {
        bool messaggio_non_leggibile=false;
    
        const uint8_t* rawData = wifiDataCharacteristic.value();
        int len = wifiDataCharacteristic.valueLength();
        Serial.print("=== DATI RICEVUTI RAW===");
        for (int i = 0; i < len; i++) {
          Serial.print(rawData[i]);
          Serial.print(" ");
        }
        Serial.println();

        String ssid = "";
        String password = "";
        bool useStored =false;

        if (len<8) {
          Serial.print("Messaggio troppo corto: lunghezza = ");
          Serial.println(len);
          messaggio_non_leggibile=true;
        }
        else
        {
          if (rawData[0] != '#' || rawData[len - 1] != '*') {
            Serial.println("Messaggio malformato (manca # o *)");
            messaggio_non_leggibile=true;
          }
          else
          {
            int index = 1;  // salta '#'

            // 1. Lunghezza SSID
            uint8_t ssidLen = rawData[index++];
            if (rawData[index++] != ':') {
              Serial.println("Separatore SSID mancante");
              messaggio_non_leggibile=true;
            }
            else
            {
              // 2. SSID
              for (int i = 0; i < ssidLen; i++) {
                if(!messaggio_non_leggibile && index<len)
                {
                  ssid += (char)rawData[index++];
                }
                else
                {
                  messaggio_non_leggibile=true;
                }
              }

              // 3. Separatore tra SSID e password
              if (rawData[index++] != ';') {
                Serial.println("Separatore SSID-PASS mancante");
                messaggio_non_leggibile=true;
              }
              else
              {
                // 4. Lunghezza password
                uint8_t passLen = rawData[index++];
                if (rawData[index++] != ':') {
                  Serial.println("Separatore password mancante");
                  messaggio_non_leggibile=true;
                }
                else
                {
                  // 5. Password
                  String password = "";
                  for (int i = 0; i < passLen; i++) {
                    if(!messaggio_non_leggibile && index<len)
                    {
                      password += (char)rawData[index++];
                    }
                    else
                    {
                      messaggio_non_leggibile=true;
                    }
                  }

                  // 6. Separatore con flag
                  if (rawData[index++] != ';') {
                    Serial.println("Separatore finale mancante");
                    messaggio_non_leggibile=true;
                  }
                  else
                  {
                      // 7. Flag usa password salvata
                      bool useStored = (rawData[index++] == 1);

                      // 8. Fine messaggio
                      if (rawData[index++] != '*') {
                        Serial.println("Messaggio non termina con *");
                        messaggio_non_leggibile=true;
                      }
                      else
                      {
                        // 🔍 Stampa tutto per verifica
                        Serial.println("=== DATI RICEVUTI ===");
                        Serial.print("SSID: ");
                        Serial.println(ssid);
                        Serial.print("Password: ");
                        Serial.println(password);
                        Serial.print("Usa password salvata: ");
                        Serial.println(useStored ? "SÌ" : "NO");
                        Serial.println("=====================");
                      }
                  }
                }
              }
            }
          }
        }
        if(messaggio_non_leggibile)
        {
          statoConnessioneWifi=(uint8_t)3;
          inviaStatoConnessione(statoConnessioneWifi);  // 3 = Errore nella lettura del messaggio
        }
        else
        {
          statoConnessioneWifi=(uint8_t)0;
          inviaStatoConnessione(statoConnessioneWifi);  // 0 = Connessione in corso
          Serial.println("Connessione in corso");
          delay(5000);
          // ✅ Simula successo o fallimento (qui va la vera logica WiFi)
          statoConnessioneWifi=(uint8_t)02; // Sostituisci con vera condizione

          inviaStatoConnessione(statoConnessioneWifi);  // ✅ Successo o fallimento
        }
      }
    }
    Serial.println("Connessione caduta");
  }
  delay(200);
}

void sendSSIDList() {
  // Crea una stringa con la lista SSID e i bit per la password salvata
  String dataToSend = "#";
  for (int i = 0; i < ssidCount; i++) {
    String ssid = ssidList[i];
    uint8_t len = ssid.length();
    dataToSend += String(len) + ":" + ssid + ":" + (passwordSaved[i] ? "1" : "0");
    if (i < ssidCount - 1) {
      dataToSend += ";";
    }
  }
  dataToSend += "*";

  // Converte in un array di byte e invia
  uint8_t data[dataToSend.length()];
  dataToSend.getBytes(data, dataToSend.length());
  
  ssidListCharacteristic.writeValue(data, dataToSend.length());
  Serial.println("Lista SSID inviata: ");
  Serial.println(dataToSend);
}

void inviaStatoConnessione(byte stato) {
  switch (stato) {
    case 0:
    {
      uint8_t response[4] = {'#', 0, '*'};
      wifiConnectionStatusCharacteristic.writeValue(response, 3);
    }
    break;
    case 1:
    {
      uint8_t response[8] = {'#', 1, ';', ip[0], ip[1], ip[2], ip[3], '*'};
      wifiConnectionStatusCharacteristic.writeValue(response, 8);
    }
    break;
    case 2:
    {
      uint8_t response[4] = {'#', 2, '*'};
      wifiConnectionStatusCharacteristic.writeValue(response, 3);
    }
    break;
  }
}

```
