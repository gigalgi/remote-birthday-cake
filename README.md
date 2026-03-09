# 🎂 remote-candle-blower

**Blow out a birthday candle over a video call — a microphone taped to the speaker detects the sound of someone blowing remotely, a servo-driven fan extinguishes the candle, and a flame sensor confirms it went out.**

[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)
![Platform](https://img.shields.io/badge/Platform-Arduino-blue)
![Language](https://img.shields.io/badge/Language-C%2B%2B%20%7C%20Arduino-orange)
[![Demo](https://img.shields.io/badge/Demo-YouTube-red)](https://www.youtube.com/watch?v=UU04qm6foCM)

**🌐 Language / Idioma:** &nbsp; [🇬🇧 English](#-english) &nbsp;|&nbsp; [🇨🇴 Español](#-español)

---

## 🇬🇧 English

### How it works

The key insight is simple: **a video call already transmits audio**. Place a microphone against the speaker of the device receiving the call — when the remote person blows, the audio comes through the speaker, the microphone picks it up, and the Arduino treats it exactly as if someone blew in the room.

```
Remote person blows
        │
        ▼ (video call audio)
  Speaker on local device
        │
        ▼ (mic taped to speaker)
  Microphone (A0)
        │  analog read > soundLimit (400)
        ▼
  Peak counter — sustained sound over 2-second window
        │  countPeak > 1000
        ▼
  Servo (pin 3) → Fan activation sequence
        │
        ▼
  Flame sensor (pin 2) confirms candle is out
```

**Sound detection logic:** The firmware uses a **peak-counting approach** rather than a simple threshold trigger to avoid false positives from ambient noise. When sound exceeds the threshold, a 2-second window opens and the system counts how many samples exceed the limit. Only if the count surpasses `peakHighTime` (1000 peaks) — meaning sustained, continuous blowing — does the fan activate. A short noise spike will not trigger it.

**Fan activation sequence:** The servo pulses the fan on and off several times to maximize airflow and ensure the flame is extinguished even if the first burst misses.

---

### Hardware

| Component | Pin | Notes |
|---|---|---|
| Microphone (analog) | A0 | Taped against the speaker — see schematic |
| Flame sensor | Digital 2 | HIGH = flame detected; LOW = candle out |
| Servo motor (fan) | PWM 3 | `write(50)` = fan ON, `write(0)` = fan OFF |
| Status LED | Digital 13 | ON during fan activation sequence |

---

### Setup & Calibration

**1. Physical placement — this is critical:**
Place the microphone as close as possible to the speaker on the device receiving the video call. The closer it is, the more reliably it picks up the remote audio. See the photos in the `Esquematico/` folder.

**2. Adjust `soundLimit`** (default: `400`) to match your microphone sensitivity:

```cpp
const int soundLimit = 400;
```

Open the Serial Monitor at 9600 baud before the candle is lit and note the baseline reading. Set `soundLimit` above the noise floor but below the level recorded when you blow. Typical values range from 300 to 600 depending on the microphone module.

**3. Adjust `peakHighTime`** (default: `1000`) to tune how long the blow must be sustained:

```cpp
const int peakHighTime = 1000;
```

A higher value requires a longer, more sustained blow. Lower it if the fan is not triggering reliably.

**4. Tune the fan sequence** if your fan/servo combination differs:

```cpp
fanOnOff.write(50);  // ON angle — adjust for your servo/fan
fanOnOff.write(0);   // OFF angle
```

Modify the pulse pattern in `loop()` to match the torque and airflow characteristics of your specific fan.

---

### Project Structure

```
├── Firmware/
│   └── velaremoto.ino        # Full Arduino firmware
├── Esquematico/
│   ├── esquematico.pdf        # Wiring schematic (PDF)
│   ├── IMG_0050.JPG           # Build photos
│   ├── IMG_0052.JPG
│   ├── IMG_0450.JPG
│   └── IMG_0451.JPG
└── LICENSE
```

---

### Dependencies

Install via Arduino Library Manager:

- **Servo** — included in the Arduino IDE by default

---

[🇨🇴 Leer en Español](#-español)

---

## 🇨🇴 Español

[🇬🇧 Read in English](#-english)

### Cómo funciona

La clave del proyecto es simple: **una videollamada ya transmite audio**. Coloca un micrófono pegado al parlante del dispositivo que recibe la llamada — cuando la persona remota sopla, el audio llega por el parlante, el micrófono lo capta, y el Arduino lo interpreta exactamente como si alguien hubiera soplado en la misma habitación.

```
Persona remota sopla
        │
        ▼ (audio de videollamada)
  Parlante del dispositivo local
        │
        ▼ (micrófono pegado al parlante)
  Micrófono (A0)
        │  lectura analógica > soundLimit (400)
        ▼
  Contador de picos — sonido sostenido en ventana de 2 segundos
        │  countPeak > 1000
        ▼
  Servo (pin 3) → Secuencia de activación del ventilador
        │
        ▼
  Sensor de llama (pin 2) confirma que la vela se apagó
```

**Lógica de detección de sonido:** El firmware usa un **conteo de picos** en lugar de un simple umbral para evitar falsos positivos por ruido ambiental. Cuando el sonido supera el umbral, se abre una ventana de 2 segundos y el sistema cuenta cuántas muestras superan el límite. Solo si el conteo supera `peakHighTime` (1000 picos) — es decir, un soplido continuo y sostenido — se activa el ventilador. Un pico de ruido breve no lo disparará.

**Secuencia de activación del ventilador:** El servo pulsa el ventilador varias veces para maximizar el flujo de aire y asegurar que la llama se apague incluso si la primera ráfaga no alcanza la vela.

---

### Hardware

| Componente | Pin | Notas |
|---|---|---|
| Micrófono (analógico) | A0 | Pegado al parlante — ver esquemático |
| Sensor de llama | Digital 2 | HIGH = llama detectada; LOW = vela apagada |
| Servo motor (ventilador) | PWM 3 | `write(50)` = ventilador ON, `write(0)` = OFF |
| LED de estado | Digital 13 | Encendido durante la secuencia de activación |

---

### Configuración y calibración

**1. Posicionamiento físico — esto es fundamental:**
Coloca el micrófono lo más cerca posible al parlante del dispositivo que recibe la videollamada. Cuanto más cerca esté, más confiable será la detección. Ver las fotos en la carpeta `Esquematico/`.

**2. Ajusta `soundLimit`** (valor por defecto: `400`) según la sensibilidad de tu micrófono:

```cpp
const int soundLimit = 400;
```

Abre el Monitor Serial a 9600 baudios antes de encender la vela y observa la lectura base. Fija `soundLimit` por encima del ruido de fondo pero por debajo del nivel registrado cuando soplas. Los valores típicos van de 300 a 600 según el módulo de micrófono.

**3. Ajusta `peakHighTime`** (valor por defecto: `1000`) para controlar cuánto tiempo debe sostenerse el soplido:

```cpp
const int peakHighTime = 1000;
```

Un valor mayor exige un soplido más largo y sostenido. Redúcelo si el ventilador no se activa con facilidad.

**4. Ajusta la secuencia del ventilador** si tu combinación de servo/ventilador es diferente:

```cpp
fanOnOff.write(50);  // Ángulo ON — ajusta para tu servo/ventilador
fanOnOff.write(0);   // Ángulo OFF
```

Modifica el patrón de pulsos en `loop()` según las características de torque y flujo de aire de tu ventilador específico.

---

### Estructura del proyecto

```
├── Firmware/
│   └── velaremoto.ino        # Firmware completo de Arduino
├── Esquematico/
│   ├── esquematico.pdf        # Esquemático de conexiones (PDF)
│   ├── IMG_0050.JPG           # Fotos del montaje
│   ├── IMG_0052.JPG
│   ├── IMG_0450.JPG
│   └── IMG_0451.JPG
└── LICENSE
```

---

### Dependencias

Instalar desde el Gestor de Librerías de Arduino:

- **Servo** — incluida por defecto en el IDE de Arduino

---

## Author / Autor

**Gilberto Galvis Giraldo**

---

## License / Licencia

MIT — see [LICENSE](LICENSE) for details.
