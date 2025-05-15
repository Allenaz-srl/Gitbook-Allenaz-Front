# Specifica Comunicazione Bluetooth

La comunicazione bluetooth è gestito da un'unico servizio, che include varie "caratteristiche", ognuna con uno specifico scopo e struttura del messaggio.

#### Convenzioni Generali

* Ogni messaggio inizia con # e finisce con \* (stessa convenzione dei messaggi via Serial tra HMI e Master).
* Le **flag** (es. booleani) **e valori numerici** ( stati e indirizzi IP) sono senpre inferiori ad a 256 e sono quindi inviati come **singolo byte.**
* Le **stringhe** sono trasmesse con la struttura seguente:
  * intero da 1 byte con lunghezza della stringa
  * &#x20; : come separatore
  * una sequenza di interi da 1 byte corrispondenti al codice ASCII dei singoli caratteri
  * se è prevista un'altra stringa -> ; come separatore
* Se una delle caratteristiche precedentemente descritta mancano(# \* : ; etc) il messaggio viene scartato come corrotto o errato, nell'app viene generata un'eccezione e viene riportata via bluetooth al server.&#x20;
* Ogni messaggio necessita di essere testato nel codice per verificare il rispetto dele convenzioni qua descritte.Se una delle caratteristiche precedentemente descritta mancano(# \* : ; etc) il messaggio viene scartato come corrotto o errato, nell'app viene generata un'eccezione e viene riletta l'informazione dal server o viene inviato in nessagio di ricezione errata dal server. Deve essere implementato un numero massimo di ritentativi, dopo il quale l'app segnala l'errore.

## Caratteristiche BLE

<table><thead><tr><th width="83.66668701171875">UUID end</th><th width="188.4444580078125">Nome</th><th>Descrizione</th><th>Chi scrive/legge</th></tr></thead><tbody><tr><td>0001</td><td>Bluetooth Service</td><td>Servizio primario</td><td>-</td></tr><tr><td>0002</td><td>WiFi Data</td><td>Invia SSID + password</td><td>App → Raspberry</td></tr><tr><td>0003</td><td>SSID List</td><td>Elenco reti Wi-Fi + flag password salvata o hotspot</td><td>Raspberry → App</td></tr><tr><td>0004</td><td>Connection Status</td><td>Stato connessione (0, 1, 2, 3) + IP (opzionale)</td><td>Raspberry → App</td></tr><tr><td>0005</td><td>WiFi Name</td><td>Nome Wi-Fi connesso</td><td>Raspberry → App</td></tr></tbody></table>

#### 📤 Caratteristica: WiFi Data

<table data-header-hidden><thead><tr><th width="160"></th><th></th></tr></thead><tbody><tr><td><strong>Campo</strong></td><td><strong>Valore</strong></td></tr><tr><td><strong>UUID</strong></td><td><code>12345678-1234-5678-1234-56789abc0002</code></td></tr><tr><td><strong>Tipo</strong></td><td>Write</td></tr><tr><td><strong>Scrittura da</strong></td><td>App</td></tr><tr><td><strong>Descrizione</strong></td><td>Invia SSID, flag <code>use_saved</code> e eventualmente password.</td></tr></tbody></table>

**Struttura del messaggio:**

```
#<L1>:<SSID_ASCII>;<FLAG>;<L2>:<PASSWORD_ASCII>*  
```

* `L1` e `L2`: lunghezze in byte delle stringhe SSID e PASSWORD
* `FLAG`: 1 byte (valore `0` o `1`)

**Esempio** (SSID: "HomeNet", Flag: 0, Pass: "myPass123"):

```
#7:72 111 109 101 78 101 116;0;9:109 121 80 97 115 115 49 50 51*
```

**Esempio** (SSID: "HomeNet", Flag: 1):

```
#7:72 111 109 101 78 101 116;1*
```

***

#### 📥 Caratteristica: Lista SSID

<table data-header-hidden><thead><tr><th width="160"></th><th></th></tr></thead><tbody><tr><td><strong>Campo</strong></td><td><strong>Valore</strong></td></tr><tr><td><strong>UUID</strong></td><td><code>12345678-1234-5678-1234-56789abc0003</code></td></tr><tr><td><strong>Tipo</strong></td><td>Read</td></tr><tr><td><strong>Scrittura da</strong></td><td>Dispositivo (BLE)</td></tr><tr><td><strong>Descrizione</strong></td><td>Lista delle reti WiFi disponibili + flag password salvata</td></tr></tbody></table>

**Struttura del messaggio:**

```
#<L1>:<SSID1_ASCII><FLAG1>;<L2>:<SSID2_ASCII><FLAG2>;...*
```

* Ogni SSID ha una lunghezza + ASCII, seguito da un **byte singolo per il flag** (`0` o `1` o `2`)
  * 0 -> password non salvata
  * 1 -> password salvata e utilizzabile
  * 2-> hotspot: password non modificabile
* Nessun `:` dopo il flag

**Esempio** (HomeNet con password, GuestNet senza):

```
#7:72 111 109 101 78 101 116 1;8:71 117 101 115 116 78 101 116 0*
```

***

#### 📶 Caratteristica: Stato Connessione WiFi

<table data-header-hidden><thead><tr><th width="160"></th><th></th></tr></thead><tbody><tr><td><strong>Campo</strong></td><td><strong>Valore</strong></td></tr><tr><td><strong>UUID</strong></td><td><code>12345678-1234-5678-1234-56789abc0004</code></td></tr><tr><td><strong>Tipo</strong></td><td>Read</td></tr><tr><td><strong>Scrittura da</strong></td><td>Dispositivo (BLE)</td></tr><tr><td><strong>Descrizione</strong></td><td>Riporta stato connessione e IP se disponibile</td></tr></tbody></table>

**Struttura del messaggio:**

```
#<STATUS>;<IP_BYTES>* 
```

* `STATUS`: singolo byte (`0` = connessione in corso, `1` = connessione riuscita, `2` = non connesso, `3` = errore nel messaggio **WiFi Data**)
* `;` separa lo stato dall’IP
* `IP_BYTES`: 4 byte grezzi **senza separatori** (solo presenti se `STATUS == 1`)
* Nessuna lunghezza o codifica per l’IP

**Esempi:**

*   🔄 **Connessione in corso** (`STATUS = 0`, nessun IP):

    ```
    #0*
    ```
*   ✅ **Connessione riuscita** (`STATUS = 1`, IP = `192.168.1.42`):

    ```
    #1;[0xC0][0xA8][0x01][0x2A]*
    ```

    > In esadecimale: `C0 A8 01 2A`
*   ❌ **Non connesso** (`STATUS = 2`, nessun IP):

    ```
    #2*
    ```
*   ❌ **Errore nel messaggio WiFi Data**(`STATUS = 3`, nessun IP):

    ```
    #3*
    ```

***

#### 📡 Caratteristica: Nome Rete Corrente

<table data-header-hidden><thead><tr><th width="160"></th><th></th></tr></thead><tbody><tr><td><strong>Campo</strong></td><td><strong>Valore</strong></td></tr><tr><td><strong>UUID</strong></td><td><code>12345678-1234-5678-1234-56789abc0005</code></td></tr><tr><td><strong>Tipo</strong></td><td>Read</td></tr><tr><td><strong>Scrittura da</strong></td><td>Dispositivo (BLE)</td></tr><tr><td><strong>Descrizione</strong></td><td>Nome rete WiFi attualmente connessa</td></tr></tbody></table>

**Struttura del messaggio:**

```
rubyCopiaModifica#<L>:<SSID_ASCII>*
```

**Esempio** (SSID: "HomeNet"):

```
bashCopiaModifica#7:72 111 109 101 78 101 116*
```
