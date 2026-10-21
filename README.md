<p align="center">
  <b><a href="#-english">🇬🇧 English</a></b> &nbsp;•&nbsp; <b><a href="#-deutsch">🇩🇪 Deutsch</a></b>
</p>

---

<a name="-english"></a>
# ESPEasy Dew Point Ventilation Control (Basement & Garage)

A smart, fully localized, and offline-capable ventilation control system based on **ESPEasy** and an **ESP8266 / ESP32**, specifically designed to effectively dehumidify basements and garages without the risk of condensation and mold. 
It uses physically exact **dew point calculation** to strictly ventilate only when the outside air is genuinely drier than the inside air. The telemetry data is sent via an efficient HTTP GET interface directly to a **ThingSpeak IoT Dashboard**.

---

## 🛑 The Problem: The "Summer Condensation" Trap

Many people intuitively try to ventilate their cool basements on warm summer days. This is a fatal mistake that directly leads to mold:

* Warm air can store exponentially more water vapor than cold air.
* At **30 °C and 45% rel. humidity**, outside air contains approx. **$13.7\text{ g/m}^3$** of water (dew point approx. 16.8 °C).
* A cool basement room might have **16 °C and 75% rel. humidity**, which represents only about **$10.2\text{ g/m}^3$** of water (dew point approx. 11.5 °C).
* **The consequence of wrong ventilation:** When the supposedly "dry" 45% outside air flows into the 16 °C cold basement, it cools down. Since its saturation capacity drops, the relative humidity shoots up to **100%** on the cold basement walls. Water condenses natively on the walls – the basement "sweats" and acute mold danger arises!

### The Solution: The Dew Point
The **Dew Point** indicates the temperature to which air would have to be cooled down for the contained moisture to precipitate as dew/condensation. It is a direct measure for the **absolute water content** of the air:
* **Outside Dew Point < Inside Dew Point**: The outside air is absolutely drier than the inside air. **Ventilation dehumidifies the room.**
* **Outside Dew Point ≥ Inside Dew Point**: Ventilation would add extra moisture to the room. **Fan stays OFF.**

---

## 🚀 Advantages of ESPEasy

[ESPEasy](https://github.com/letscontrolit/ESPEasy) is a highly flexible open-source firmware for ESP8266 and ESP32 modules:

1. **No coding required (No-Code / Low-Code):** Everything configured comfortably via the web interface.
2. **Built-in dew point calculation:** ESPEasy offers native system functions like `%c_dew_th%(Temperature, Humidity)`.
3. **Maximum reliability & autonomy:** The logic runs **completely locally and offline**.
4. **Huge sensor ecosystem:** Plug-and-play support for BME280, SHT3x, DHT22, etc.
5. **Powerful Rules Engine:** Event-based triggers, timers, hysteresis, and direct GPIO controls.

---

## 📦 Bill of Materials (BOM)

| Category | Component | Recommended Model | Description / Purpose | Approx. Cost |
| :--- | :--- | :--- | :--- | :---: |
| **Microcontroller** | ESP8266 or ESP32 | Wemos D1 Mini / NodeMCU ESP32 | Runs ESPEasy firmware, calculates dew point, controls relay | 3 – 6 € |
| **Inside Sensor** | Temp & Humidity Sensor | Bosch BME280 (I2C) or Sensirion SHT31 | Precision sensor inside the basement / garage | 4 – 8 € |
| **Outside Sensor** | Temp & Humidity Sensor | Bosch BME280 (I2C) or SHT31 | Measures outdoor air (requires weather/radiation shield) | 4 – 8 € |
| **Actuator** | Relay Module | 5V 1-Channel Relay with Optocoupler or SSR-25DA | Safely switches the 230V fan on GPIO 2 | 2 – 6 € |
| **Fan** | Inline Duct Fan / Extractor | 100 mm / 125 mm Inline Fan (230V AC or 12V DC PWM) | Exhausts moist air to the outside (~100–180 m³/h) | 15 – 45 € |
| **Backdraft Damper** | Shutter Flap | 100 mm / 125 mm gravity / spring backdraft damper | Prevents backflow of cold/humid outside air when idle | 5 – 10 € |
| **Power Supply** | 5V DC Power Adapter | 5V / 2A Micro-USB / USB-C adapter or DIN rail PSU | Stable 24/7 power supply for ESP and sensors | 5 – 12 € |
| **Wiring & Housing** | Enclosure & Cable | IP65 Junction Box, Cat5/6/7 cable, WAGO 221 | Safe installation and reliable connection to outdoor sensor | 5 – 15 € |
| *(Optional)* | Status Display | 0.96" I2C OLED (SSD1306) | Local display of temperatures, humidity & dew points | 3 – 5 € |
| *(Optional)* | Manual Override | 3-way switch (ON - OFF - AUTO) | Manual control for maintenance or continuous air | 2 – 4 € |

---

## 🔌 Hardware & Pinout

### Wiring / Pinout (Example: Wemos D1 Mini)

| D1 Mini Pin | ESP8266 GPIO | Target Module | Module Pin | Function |
| :--- | :--- | :--- | :--- | :--- |
| **3V3** | 3.3V | BME280 / SHT31 (Indoor & Outdoor) | **VCC / VIN** | 3.3V power supply for sensors |
| **GND** | GND | All Modules (Sensors & Relay) | **GND** | Common ground |
| **D1** | **GPIO 5** | BME280 / SHT31 (Indoor & Outdoor) | **SCL** | I2C Clock line |
| **D2** | **GPIO 4** | BME280 / SHT31 (Indoor & Outdoor) | **SDA** | I2C Data line |
| **D4** | **GPIO 2** | Relay Module / SSR | **IN** | Control signal for fan relay (`1` = ON, `0` = OFF) |
| **5V / VBUS** | 5.0V | Relay Module | **VCC** | 5V power supply for relay coil |

### Alternative: ESP32 NodeMCU

| ESP32 Pin | GPIO | Target Module | Module Pin | Function |
| :--- | :--- | :--- | :--- | :--- |
| **3V3** | 3.3V | Sensors (Indoor & Outdoor) | **VCC** | 3.3V sensor supply |
| **GND** | GND | All Modules | **GND** | Common ground |
| **D22** | **GPIO 22** | Sensors (Indoor & Outdoor) | **SCL** | Default I2C Clock |
| **D21** | **GPIO 21** | Sensors (Indoor & Outdoor) | **SDA** | Default I2C Data |
| **D2** | **GPIO 2** | Relay Module | **IN** | Relay control signal (matches Rules code) |
| **VIN / 5V** | 5.0V | Relay Module | **VCC** | 5V relay supply |

> [!TIP]
> **Two BME280 Sensors on the Same I2C Bus:**  
> By default, the BME280 uses I2C address `0x76`. Connect the **SDO** pin of the second (outdoor) sensor to **3.3V** (or bridge the solder pad) to change its address to `0x77`. Alternatively, an **SHT31** sensor (address `0x44`) can be used for the outdoor reading.

---

## 🛠️ Step-by-Step Installation

### 1. Configure ESPEasy Tasks (Devices)

Create three tasks in the ESPEasy web interface under **Devices**:
* **Task 1 (Inside):** device (e.g. BME280), name `Innen`, values `Temperature`, `Humidity`.
* **Task 2 (Outside):** device (e.g. BME280), name `Aussen`, values `Temperature`, `Humidity`.
* **Task 3 (Dummy Device for Dew points):** Generic - Dummy Device, name `Taupunkt`, values `Aussen`, `Innen`.

### 2. Add Rules Code

Enable Rules under **Tools** -> **Advanced**. Paste the following code into the **Rules 1** tab:

```text
on System#Boot do
  GPIO,2,0
  Let,1,0
endon

On Innen#Humidity Do
  // 1. Calculate dew points (%c_dew_th%)
  TaskValueSet,3,1,%c_dew_th%([Aussen#Temperature],[Aussen#Humidity])
  TaskValueSet,3,2,%c_dew_th%([Innen#Temperature],[Innen#Humidity])

  // 2. Calculate dew point difference
  Let,2,[Taupunkt#Innen]-[Taupunkt#Aussen]

  // 3. Smart hysteresis (humidity > 50% AND dew point diff > 2°C)
  if [Innen#Humidity] > 50 and [VAR#2] > 2
    GPIO,2,1
    Let,1,1
  else
    GPIO,2,0
    Let,1,0
  endif

  // 4. Send telemetry data to ThingSpeak
  SendToHTTP api.thingspeak.com,80,"/update?api_key=YOUR_THINGSPEAK_WRITE_API_KEY&field2=[Aussen#Temperature]&field3=[Aussen#Humidity]&field4=[Innen#Temperature]&field5=[Innen#Humidity]&field6=[VAR#1]&field7=[Taupunkt#Aussen]&field8=[Taupunkt#Innen]"
endon
```
> [!IMPORTANT]
> Replace `YOUR_THINGSPEAK_WRITE_API_KEY` with your actual ThingSpeak API Key!

---

<a name="-deutsch"></a>
# 🌬️ ESPEasy Taupunktlüftung & ThingSpeak Cloud-Monitoring

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform: ESP8266 / ESP32](https://img.shields.io/badge/Platform-ESP8266%20%7C%20ESP32-blue.svg)](https://espeasy.readthedocs.io/)
[![Firmware: ESPEasy](https://img.shields.io/badge/Firmware-ESPEasy%20Rules-green.svg)](https://github.com/letscontrolit/ESPEasy)
[![Cloud: ThingSpeak](https://img.shields.io/badge/Cloud-ThingSpeak-orange.svg)](https://thingspeak.mathworks.com/)
[![Release: v1.0.0](https://img.shields.io/badge/Release-v1.0.0-blue.svg)](https://github.com/juergen874/espeasy-taupunkt-lueftung/releases)

Intelligente, autarke Lüftungs- und Entfeuchtungssteuerung für Keller, Garagen, Dachböden und Wohnräume auf Basis von **ESP8266 / ESP32**, **ESPEasy Rules** und **ThingSpeak IoT-Telemetrie**.

Das System berechnet kontinuierlich den **Taupunkt** für Innen- und Außenbereich und schaltet den Lüfter nur dann ein, wenn die einströmende Außenluft den Raum nachweislich **entfeuchtet** und keine gefährliche Feuchtigkeitskondensation (Schimmelbildung) erzeugt.

---

## 📑 Inhaltsverzeichnis

- [Warum Taupunktlüftung? (Physikalischer Hintergrund)](#-warum-taupunktlüftung-physikalischer-hintergrund)
- [Vorteile von ESPEasy](#-vorteile-von-espeasy)
- [Vorteile von ThingSpeak](#-vorteile-von-thingspeak)
- [Funktionsweise der Steuerung](#-funktionsweise-der-steuerung)
- [Hardware & Anschlüsse](#-hardware--anschlüsse)
- [Materialliste (BOM / Einkaufsliste)](#-materialliste-bom--einkaufsliste)
- [Schritt-für-Schritt Installation](#-schritt-für-schritt-installation)
  - [1. ESPEasy Tasks (Geräte) konfigurieren](#1-espeasy-tasks-geräte-konfigurieren)
  - [2. Rules-Code hinterlegen](#2-rules-code-hinterlegen)
  - [3. ThingSpeak Kanal einrichten](#3-thingspeak-kanal-einrichten)
- [Individuelle Anpassungsmöglichkeiten](#-individuelle-anpassungsmöglichkeiten)
- [Lizenz](#-lizenz)

---

## 🌡️ Warum Taupunktlüftung? (Physikalischer Hintergrund)

Viele einfache Lüftungssteuerungen vergleichen lediglich die relative Luftfeuchtigkeit (z. B. *„Wenn innen > 60 % und außen < 70 %, dann lüften“*) oder multiplizieren Temperatur und Feuchte ($T \times RH$). **Das ist im Sommer gefährlich!**

### Das Problem der Sommerkondensation („Keller-Falle“):
* Warme Luft kann exponentiell mehr Wasserdampf speichern als kalte Luft.
* Bei **30 °C und 45 % rel. Feuchte** enthält Außenluft ca. **$13{,}7\text{ g/m}^3$** Wasser (Taupunkt ca. 16,8 °C).
* Ein kühler Kellerraum hat vielleicht **16 °C und 75 % rel. Feuchte**, was nur ca. **$10{,}2\text{ g/m}^3$** Wasser entspricht (Taupunkt ca. 11,5 °C).
* **Die Folge einer falschen Lüftung:** Strömt die vermeintlich „trockene“ 45%-Außenluft in den 16 °C kalten Keller, kühlt sie ab. Da ihre Sättigungsgrenze sinkt, schießt die relative Feuchte an den kalten Kellerwänden auf **100 %**. Wasser kondensiert flüssig an den Wänden – der Keller „schwitzt“ und es entsteht akute Schimmelgefahr!

### Die Lösung: Der Taupunkt
Der **Taupunkt (Dew Point)** gibt an, auf welche Temperatur Luft abgekühlt werden müsste, damit die enthaltene Feuchtigkeit als Tau/Kondensat ausfällt. Er ist ein direktes Maß für den **absoluten Wassergehalt** der Luft:
* **Außen-Taupunkt < Innen-Taupunkt**: Die Außenluft ist absolut trockener als die Innenluft. **Lüften entfeuchtet den Raum.**
* **Außen-Taupunkt ≥ Innen-Taupunkt**: Lüften würde dem Raum zusätzliche Feuchtigkeit zuführen. **Lüfter bleibt AUS.**

---

## 🚀 Vorteile von ESPEasy

[ESPEasy](https://github.com/letscontrolit/ESPEasy) ist eine extrem flexible Open-Source-Firmware für ESP8266- und ESP32-Module:

1. **Kein Programmieren nötig (No-Code / Low-Code):**
   * Kein Hantieren mit Arduino IDE, C++-Bibliotheken oder Compiler-Fehlern.
   * Einmaliges Flashen der Standard-Firmware; alle Sensoren und Regeln werden bequem über das responsive Webinterface konfiguriert.
2. **Eingebaute Taupunkt-Berechnung:**
   * ESPEasy bietet native Systemfunktionen wie `%c_dew_th%(Temperatur, Feuchte)`.
   * Die physikalisch exakte Magnus-Formel wird direkt auf dem Mikrocontroller berechnet – ohne externe Server oder Cloud-Abhängigkeit.
3. **Maximale Ausfallsicherheit & Autarkie:**
   * Die Lüftungslogik läuft **vollständig lokal und offline**. Fällt das Internet oder der WLAN-Router aus, steuert der ESP den Lüfter zuverlässig weiter.
   * Robuster Hardware-Watchdog und automatische Reconnect-Mechanismen sorgen für monatelangen Dauerbetrieb ohne Abstürze.
4. **Riesiges Sensor-Ökosystem:**
   * Plug-and-Play-Unterstützung für hunderte Sensoren: BME280 (I2C), SHT30/31/35, DHT22, DS18B20 und viele mehr.
5. **Mächtige Rules-Engine:**
   * Ereignisbasierte Trigger (`On ... Do ... Endon`), Timer, Hysteresen, interne Variablen (`[VAR#1]`, `Let`) und direkte GPIO-Schaltungen.

---

## 📊 Vorteile von ThingSpeak

[ThingSpeak](https://thingspeak.mathworks.com/) ist die renommierte IoT-Cloud-Plattform von **MathWorks** (Entwickler von MATLAB):

1. **Einfachstes Datenlogging per HTTP GET:**
   * Ein einfacher Befehl (`SendToHTTP api.thingspeak.com,80,"/update?api_key=...&field2=..."`) genügt.
   * Minimaler CPU- und Speicheraufwand für den Mikrocontroller, keine schweren TLS/MQTT-Stacks erforderlich.
2. **Kostenlos & Wartungsfrei:**
   * Kein eigener Server, kein Raspberry Pi, kein Docker und keine Datenbank-Administration (wie InfluxDB/Grafana) nötig.
3. **Automatische Visualisierung & Dashboards:**
   * Automatisch generierte Verlaufsdiagramme für Temperatur, Luftfeuchtigkeit, Taupunkte und Lüfterschaltzyklen.
   * Einfach per Browser oder ThingSpeak-App auf jedem Smartphone abrufbar.
4. **Direkte MATLAB-Integration:**
   * Komplexe Analysen, Korrelationen und Visualisierungen können direkt in ThingSpeak mit Online-MATLAB-Skripten ausgeführt werden.
5. **Alarmierungen & Webhooks:**
   * Benachrichtigungen via ThingSpeak Alerts (E-Mail, Webhook) bei außergewöhnlichen Ereignissen (z. B. Sensor defekt oder kritische Feuchtewerte).

---

## ⚙️ Funktionsweise der Steuerung

1. **Boot:** Relais an GPIO 2 wird definiert auf `0` (AUS) gesetzt.
2. **Trigger:** Sobald der Raum-Sensor (`Innen#Humidity`) einen neuen Messwert liefert, startet die Logik.
3. **Berechnung:**
   * Taupunkt Außen: `%c_dew_th%([Aussen#Temperature],[Aussen#Humidity])`
   * Taupunkt Innen: `%c_dew_th%([Innen#Temperature],[Innen#Humidity])`
   * Differenz: $\Delta TP = \text{Taupunkt}_{\text{Innen}} - \text{Taupunkt}_{\text{Aussen}}$
4. **Schaltentscheidung:**
   * Lüfter geht nur **AN** (`GPIO,2,1`), wenn:
     1. Die Raumluftfeuchte über dem Zielwert liegt: `[Innen#Humidity] > 50` (%)
     2. Die Außenluft deutlich trockener ist (Hysterese): `[VAR#2] > 2` (°C)
   * Andernfalls bleibt der Lüfter **AUS** (`GPIO,2,0`).
5. **Telemetrie:** Übertragung der Messwerte, Taupunkte und des Lüfterstatus an ThingSpeak.

---

## 🔌 Hardware & Anschlüsse

### Anschluss- & Pinbelegung (am Beispiel Wemos D1 Mini)

| D1 Mini Pin | GPIO | Komponente | Beschreibung |
| :--- | :--- | :--- | :--- |
| **3V3** | 3.3V | VCC BME280 / SHT31 | Versorgungsspannung der Sensoren (3,3 V) |
| **GND** | GND | GND aller Module | Gemeinsame Masse |
| **D1** | GPIO 5 | SCL (I2C Clock) | Taktleitung für Innen- und Außensensor |
| **D2** | GPIO 4 | SDA (I2C Data) | Datenleitung für Innen- und Außensensor |
| **D4** | GPIO 2 | IN (Relais-Signal) | Steuersignal für Relaismodul / SSR (High = Ein) |
| **5V / VBUS**| 5.0V | VCC Relaismodul | Versorgungsspannung für Relaisspule (5 V) |

> [!TIP]
> **Zwei BME280 Sensoren am selben I2C-Bus:**  
> Standardmäßig belegt der BME280 die I2C-Adresse `0x76`. Bei einem der beiden Sensoren (z. B. dem Außensensor) verbindet man den Pin **SDO** mit **3.3V** (oder setzt die entsprechende Lötbrücke), um dessen Adresse auf `0x77` umzustellen. Alternativ kann als zweiter Sensor ein **SHT31** (Adresse `0x44`) verwendet werden.

---

## 📦 Materialliste (BOM / Einkaufsliste)

Für den Aufbau der automatischen Taupunktlüftung wird folgende Hardware benötigt:

### 1. Steuerung & Elektronik
| Bauteil | Empfehlung / Spezifikation | Stückzahl | Zweck | Richtpreis |
| :--- | :--- | :---: | :--- | :---: |
| **Mikrocontroller** | Wemos D1 Mini (ESP8266) oder NodeMCU ESP32 | 1 | Führt ESPEasy autark aus, liest Sensoren und schaltet Relais | 3 – 6 € |
| **Relais-Modul** | 5V 1-Kanal Relais mit Optokoppler (z. B. Songle) oder Solid-State-Relais (SSR-25DA) | 1 | Schaltet den 230V Lüfter potentialfrei und sicher | 2 – 6 € |
| **Netzteil** | 5V / 2A Micro-USB / USB-C Netzteil oder MeanWell HDR-15-5 Hutschienennetzteil | 1 | Stabile, langlebige Spannungsversorgung im 24/7 Dauerbetrieb | 5 – 12 € |

### 2. Sensorik
| Bauteil | Empfehlung / Spezifikation | Stückzahl | Zweck | Richtpreis |
| :--- | :--- | :---: | :--- | :---: |
| **Sensor Innen** | Bosch BME280 (I2C) oder Sensirion SHT31 | 1 | Exakte Erfassung von Raumtemperatur und relativer Luftfeuchte | 4 – 8 € |
| **Sensor Außen** | Bosch BME280 (I2C) oder Sensirion SHT31 / SHT35 | 1 | Exakte Erfassung von Außentemperatur und relativer Luftfeuchte | 4 – 8 € |
| **Wetterschutz Außen** | Lamellengehäuse (Strahlungsschutz) oder Schutzhülse (IP-Membran) | 1 | Schützt den Außensensor vor direkter Sonneneinstrahlung und Regen | 5 – 12 € |

### 3. Lüftungstechnik & Mechanik
| Bauteil | Empfehlung / Spezifikation | Stückzahl | Zweck | Richtpreis |
| :--- | :--- | :---: | :--- | :---: |
| **Lüfter / Ventilator** | 230V Rohrventilator (100 mm oder 125 mm) z. B. Blauberg / Rohreinschublüfter | 1 | Förderleistung ca. 100–180 m³/h für zügigen Luftaustausch | 20 – 50 € |
| **Rückstauklappe** | Mechanische Schwerkraft- oder Federrückschlagklappe (100/125 mm) | 1 | Verhindert unkontrolliertes Zurückströmen kalter/feuchter Luft bei Stillstand | 5 – 10 € |
| **Lüftungsrohr & Gitter** | Wickelfalzrohr / Aluflexrohr + Außengitter mit Insektenschutznetz | 1 Set | Wanddurchführung und saubere Luftführung | 10 – 20 € |

### 4. Installationsmaterial & Zubehör
| Bauteil | Empfehlung / Spezifikation | Zweck | Richtpreis |
| :--- | :--- | :--- | :---: |
| **Verteilergehäuse** | Abzweigdose IP65 / Feuchtraumgehäuse | Berührungssichere und feuchtigkeitsgeschützte Unterbringung der 230V- und 5V-Elektronik | 4 – 8 € |
| **Sensorleitung Außen** | Geschirmtes 4-adriges Kabel (z. B. Cat.5/6/7 Verlegekabel oder J-Y(St)Y 2x2x0.8) | Zuverlässige I2C-Verbindung zum Außensensor (auch über 3–10 m) | 3 – 8 € |
| **Klemmen & Kabel** | WAGO 221 Hebelklemmen, Jumper-Kabel, Schrumpfschlauch | Schnelle, wartungsfreie und berührungssichere Verdrahtung | 3 – 6 € |

### 5. Optionale Komfort-Erweiterungen
* 📺 **0.96" I2C OLED-Display (SSD1306)** (~3–5 €): Direkte Anzeige von Innen-/Außentemperatur, Luftfeuchte, Taupunkten und Lüfterstatus vor Ort.
* 🎛️ **3-Stufen-Schalter (Auto / Aus / Handbetrieb)** (~2–4 €): Manueller Not- oder Dauerbetrieb ohne Webinterface.
* 💡 **Status-LED (5V mit Vorwiderstand 330 Ω)** (~1 €): Optische Betriebsanzeige direkt an der Gehäusefront.

---

## 🛠️ Schritt-für-Schritt Installation

### 1. ESPEasy Tasks (Geräte) konfigurieren

Im ESPEasy Webinterface unter **Devices** drei Tasks anlegen:

* **Task 1 (Innen):**
  * Device: z. B. `Environment - BMx280` (oder DHT / SHT)
  * Name: `Innen`
  * Values: `Temperature`, `Humidity`
* **Task 2 (Aussen):**
  * Device: z. B. `Environment - BMx280` (oder per MQTT/HTTP von zweitem ESP)
  * Name: `Aussen`
  * Values: `Temperature`, `Humidity`
* **Task 3 (Dummy Device für Taupunkte):**
  * Device: `Generic - Dummy Device`
  * Name: `Taupunkt`
  * Simulating: `Sensor (2 Values)`
  * Value 1 Name: `Aussen`
  * Value 2 Name: `Innen`

### 2. Rules-Code hinterlegen

1. In ESPEasy auf **Tools** -> **Advanced** gehen und `Rules` aktivieren.
2. Den Tab **Rules 1** öffnen und folgenden Code einfügen:

```text
on System#Boot do
  GPIO,2,0
  Let,1,0
endon

On Innen#Humidity Do
  // 1. Taupunkte für Außen und Innen berechnen (%c_dew_th%)
  TaskValueSet,3,1,%c_dew_th%([Aussen#Temperature],[Aussen#Humidity])
  TaskValueSet,3,2,%c_dew_th%([Innen#Temperature],[Innen#Humidity])

  // 2. Taupunktdifferenz berechnen (Innen minus Außen)
  Let,2,[Taupunkt#Innen]-[Taupunkt#Aussen]

  // 3. Intelligente Schalthysterese (Innenfeuchte > 50% UND Taupunktdifferenz > 2°C)
  if [Innen#Humidity] > 50 and [VAR#2] > 2
    GPIO,2,1
    Let,1,1
  else
    GPIO,2,0
    Let,1,0
  endif

  // 4. Telemetriedaten an ThingSpeak übertragen
  SendToHTTP api.thingspeak.com,80,"/update?api_key=YOUR_THINGSPEAK_WRITE_API_KEY&field2=[Aussen#Temperature]&field3=[Aussen#Humidity]&field4=[Innen#Temperature]&field5=[Innen#Humidity]&field6=[VAR#1]&field7=[Taupunkt#Aussen]&field8=[Taupunkt#Innen]"
endon
```
> [!IMPORTANT]
> Ersetze `YOUR_THINGSPEAK_WRITE_API_KEY` durch deinen echten API-Key aus ThingSpeak!

### 3. ThingSpeak Kanal einrichten

Im [ThingSpeak](https://thingspeak.mathworks.com/) Kanal folgende Felder benennen:

| Feld | Name | Einheit |
| :--- | :--- | :--- |
| **Field 2** | `Temperatur Aussen` | °C |
| **Field 3** | `Feuchte Aussen` | % |
| **Field 4** | `Temperatur Innen` | °C |
| **Field 5** | `Feuchte Innen` | % |
| **Field 6** | `Luefter Status` | 0/1 |
| **Field 7** | `Taupunkt Aussen` | °C |
| **Field 8** | `Taupunkt Innen` | °C |

---

## 🔧 Individuelle Anpassungsmöglichkeiten

* **Schwellwert für Feuchte:**
  Im Code `if [Innen#Humidity] > 50` kann `50` durch den gewünschten Zielwert ersetzt werden (z. B. `60` für Wohnkeller).
* **Taupunktdifferenz (Hysterese):**
  Mit `[VAR#2] > 2` schaltet der Lüfter erst ein, wenn die Außenluft mindestens 2 °C trockener ist. Ein höherer Wert (z. B. 3 °C oder 4 °C) sorgt für mehr Sicherheitsabstand und verhindert schnelles Takten des Lüfters.
* **Frostschutz:**
  Um das Auskühlen im Winter zu verhindern, kann eine Mindesttemperatur ergänzt werden:
  ```text
  if [Innen#Humidity] > 50 and [VAR#2] > 2 and [Innen#Temperature] > 10
  ```

---

## 📄 Lizenz

Dieses Projekt ist unter der [MIT License](LICENSE) veröffentlicht.
