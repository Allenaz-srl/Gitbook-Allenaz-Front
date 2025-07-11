---
description: Modulo Regolazione Comandi Vocali - VoiceCommands.jsx
---

# VoiceCommands (Comandi vocali)

<figure><img src="../../../.gitbook/assets/ChatGPT Image 24 giu 2025, 17_32_41.png" alt="" width="375"><figcaption></figcaption></figure>

## 🎯 Scopo

Questo componente React permette di regolazre il comportamento del sistema di riconoscimento vocale integrato nell'esoscheletro. In particolare consente all'utente di:

* Regolare la **sensibilità del microfono**
* Regolare il **volume delle risposte audio**
* Selezionare il tipo di **risposta vocale preferita**: breve, standard o completa

I dati configurati vengono salvati e inviati(json file in backend) via WebSocket al backend Node.js, che applica i valori tramite <kbd>amixer</kbd> su Raspberry Pi.

## ⚙️ Parametri e interfaccia

| Parametro              | Tipo     | Range                         | Effetto                                                      |
| ---------------------- | -------- | ----------------------------- | ------------------------------------------------------------ |
| `1a` - Volume Risposta | Slider   | 10% – 100%                    | Regola l’uscita audio dei messaggi vocali                    |
| `1b` - Sensibilità Mic | Slider   | 10% – 100%                    | Controlla la sensibilità del microfono via `amixer`          |
| `1c` - Tipo Risposta   | Checkbox | `brief`/`standard`/`complete` | Imposta il tipo di risposta del sistema (bip, nulla, vocale) |

## 🧩 Struttura dello stato React

All'interno del componente:

```jsx
const [axisValue1, setAxisValue1] = useState(10); // Volume risposta
const [axisValue2, setAxisValue2] = useState(10); // Sensibilità microfono
const [selectedOption, setSelectedOption] = useState(null); // Tipo risposta
```

Valori iniziali vengono sincronizzati al caricamento:

```jsx
useEffect(() => {
  if (!sliderChanged) {
    if (typeof data["1a"] === "number") setAxisValue1(data["1a"]);
    if (typeof data["1b"] === "number") setAxisValue2(data["1b"]);
    if (typeof data["1c"] === "string") setSelectedOption(data["1c"]);
  }
}, [data, sliderChanged]);
```

## 🔊 Slider - Volume e Microfono

I due slider agiscono separatamente e modificano i rispettivi valori nel <kbd>useState</kbd>. Al movimento dell'utente viene attivato il flag <kbd>sliderChanger</kbd> per bloccare l'aggiornamento automatico dal backend:

```jsx
const handleChangeAxis1 = (event, newValue) => {
  setAxisValue1(newValue);
  setSliderChanged(true);
};
```

## 🔘 Selezione del tipo di risposta

Solo un'opzione puo essere attiva alla volta. Quando si seleziona una checkbox, viene aggiornata <kbd>selectedOption</kbd>:

```jsx
const handleCheckboxChange = (option) => () => {
  setSelectedOption(option);
  setSliderChanged(true);
};
```

📌 Le opzioni disponibili sono:

* <kbd>**Brief**</kbd>: un suono acustico (bip)
* <kbd>**Standard**</kbd>: nessun risposta vocale
* <kbd>**Complete**</kbd>: voce sintetica tramite TTS (es. "Ciao, come posso aiutare?")&#x20;

## 💾 Salvataggio delle impostazioni

Alla pressione del pulsante <kbd>SAVE</kbd> , viene generato un oggetto da inviare:

```jsx
const handleSave = () => {
  const dataToSave = {
    "1a": axisValue1,
    "1b": axisValue2,
    "1c": selectedOption,
  };
  sendMessage(dataToSave);
};
```

Backend riceve questo pacchetto e:

* Imposta i livelli hardware con <kbd>amixer</kbd>&#x20;
* Salva il tipo di risposta nel file di configurazione
* Applica i cambiamenti senza necessita di riavvio

## 🧪 Esempi e test

| Scenario                         | Risultato atteso                                        |
| -------------------------------- | ------------------------------------------------------- |
| Slider volume portato a 100%     | Massimo volume delle risposte audio                     |
| Slider microfono a 30%           | Ridotta sensibilità microfono                           |
| Selezione "brief"                | Ogni comando vocale riceve un suono bip                 |
| Selezione "complete"             | Alcuni comandi vocali ricevono una risposta vocale      |
| Invio dati al backend con `SAVE` | Aggiornamento in tempo reale senza ricaricare la pagina |
| Ricezione nuovi dati dal backend | Aggiorna interfaccia (solo se `sliderChanged = false`)  |

### 🗒️ **Nota tecnica**:

La codifica <kbd>"1a"</kbd>, <kbd>"1b"</kbd>, <kbd>"1c"</kbd> e utilizzata per semplificare l'identificazione lato backend (Node.js)
