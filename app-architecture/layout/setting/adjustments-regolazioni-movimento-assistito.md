---
description: Modulo di Regolazione Anatomica – Adjustments.jsx
---

# Adjustments (Regolazioni movimento assistito)

<figure><img src="../../../.gitbook/assets/ChatGPT Image 24 giu 2025, 17_17_33.png" alt="" width="375"><figcaption></figcaption></figure>

## 🎯 Scopo

Il componente <kbd>Adjustments</kbd> permette all'utente (es. terapista o operatore) di configurare **le dimensione anatomiche** del paziente - in particolare la **lunghezza e il peso** del **braccio** e **dell'avambraccio** - che saranno usate dal firmware dell'esoscheletro per l'adattamento biomeccanico.

#### 🖼️ Interfaccia

L’interfaccia è composta da **4 slider**:

| Regione     | Parametro | Range      |
| ----------- | --------- | ---------- |
| Braccio     | Lunghezza | 0 – 60 mm  |
| Braccio     | Peso      | 0 – 200 %  |
| Avambraccio | Lunghezza | 0 – 200 mm |
| Avambraccio | Peso      | 0 – 200 %  |

## 🔄 Stato iniziale e sincronizzazione

Il componente riceve valori iniziali tramite WebSocket dal backend, che li estrae dal file json.

```jsx
useEffect(() => {
  const handleDataUpdate = (data) => {
    const { adjustmentsArmData } = data;
    setData(adjustmentsArmData);
  };

  subscribeToMessages(handleDataUpdate);
  return () => unsubscribeFromMessages(handleDataUpdate);
}, []);
```

Esempio di dati ricevuti:

```json
{
  "adjustmentsArmData": {
    "90": 35,
    "91": 120,
    "92": 70,
    "93": 85
  }
}
```

Le chiavi 90-93 corrispondono ai parametri:

| Codice | Descrizione           |
| ------ | --------------------- |
| `90`   | Lunghezza Braccio     |
| `91`   | Lunghezza Avambraccio |
| `92`   | Peso Braccio          |
| `93`   | Peso Avambraccio      |

## 🛠️ Interazione dell'utente

Quando l'utente muove gli slider, lo stato viene aggiornato localmente tramite <kbd>useState</kbd> , e i flag <kbd>sliderChanged</kbd> viene attivato per evitare che i valori tornino a quelli originali in arrivo dal backend.

```jsx
const handleChangeAxis1 = (event, newValue) => {
  setAxisValue1(newValue);
  setSliderChanged(true);
};
```

## 💾 Salvataggio

Alla pressione di <kbd>SAVE</kbd> , i nuovi parametri vengono **inviati al backend tramite WebSocket**:

```jsx
const handleSave = () => {
  const dataToSave = {
    90: axisValue1,
    92: axisValue2,
    91: axisValue3,
    93: axisValue4,
  };
  sendMessage(dataToSave);
};
```

✅ Il backend inoltra questi dati al microcontrollore Master (Arduino), dove verranno utilizzati per calcoli  necessarie.

## 🔁 Ripristino valori predefiniti

Il pulsante <kbd>DEFAULT</kbd> assegna manualmente valori standard ai 4 parametri:

```jsx
const handleDefault = () => {
  setAxisValue1(10);
  setAxisValue2(10);
  setAxisValue3(10);
  setAxisValue4(10);
  setSliderChanged(true);
};
```

## 🧪 Test suggeriti

| Azione             | Comportamento atteso                           |
| ------------------ | ---------------------------------------------- |
| Spostamento slider | Visualizzazione aggiornata in tempo reale      |
| SAVE               | Invio WebSocket con chiavi `90–93`             |
| DEFAULT            | Valori impostati a `10` e aggiornamento visivo |
