# ThingSpeak Konfiguration & Felder

Dieses Dokument beschreibt die Einrichtung des ThingSpeak-Kanals für das Projekt **ESPEasy Taupunktlüftung**.

## 1. ThingSpeak Kanal anlegen

1. Melde dich bei [ThingSpeak](https://thingspeak.mathworks.com/) an (kostenloser Account reicht aus).
2. Klicke auf **Channels** -> **My Channels** -> **New Channel**.
3. Vergib einen Namen, z. B. `Kellerlüftung Taupunkt`.
4. Aktiviere und benenne die folgenden Felder (Field 2 bis Field 8):

| Feld | Feldname in ThingSpeak | Einheit | Beschreibung |
| :--- | :--- | :--- | :--- |
| **Field 1** | *(frei / optional)* | – | Nicht verwendet (oder für spätere Erweiterungen) |
| **Field 2** | `Temperatur Aussen` | `°C` | Außentemperatur des Sensors |
| **Field 3** | `Feuchte Aussen` | `%` | Relative Außenluftfeuchtigkeit |
| **Field 4** | `Temperatur Innen` | `°C` | Raumtemperatur des Sensors |
| **Field 5** | `Feuchte Innen` | `%` | Relative Raumluftfeuchtigkeit |
| **Field 6** | `Luefter Status` | `0/1` | Status des Relais (`0` = Aus, `1` = An) |
| **Field 7** | `Taupunkt Aussen` | `°C` | Von ESPEasy berechneter Taupunkt außen |
| **Field 8** | `Taupunkt Innen` | `°C` | Von ESPEasy berechneter Taupunkt innen |

5. Speichere den Kanal (**Save Channel**).

---

## 2. Write API Key kopieren

1. Wechsle im Kanal auf den Tab **API Keys**.
2. Kopiere den **Write API Key**.
3. Ersetze im ESPEasy Rules-Code den Platzhalter `YOUR_THINGSPEAK_WRITE_API_KEY` durch deinen Schlüssel.

---

## 3. Beispiel-URL für manuelle Tests (Browser / Curl)

Du kannst den Datenversand manuell im Webbrowser oder über das Terminal testen:

```http
GET https://api.thingspeak.com/update?api_key=YOUR_THINGSPEAK_WRITE_API_KEY&field2=14.5&field3=70.0&field4=18.0&field5=65.0&field6=1&field7=9.1&field8=11.3
```

Erfolgreiche Antwort von ThingSpeak ist die fortlaufende Eintragsnummer (z. B. `1`, `2`, ...).

> [!NOTE]
> ThingSpeak erlaubt im kostenlosen Tarif standardmäßig eine Aktualisierung alle **15 Sekunden**. Das Sensor-Sendeintervall in ESPEasy sollte daher auf mindestens 30 oder 60 Sekunden eingestellt sein.
