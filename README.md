# CA2 Dam Valve Monitor
A simple web dashboard for monitoring a simulated dam release-valve system. It receives live readings and status messages from a WAGO Compact Controller 100 through MQTT, using the same MQTT connection approach as the working CA2 console application.
The dashboard is for monitoring and demonstration only. It does not send commands to open a physical valve.

## Features
- Sensor indicator (DI2): A lamp shows whether the maximum water-level sensor is triggered.
- Confirmation button indicator (DI1): A lamp shows whether the physical button is pressed.
- Water-level display: A tank graphic and numerical volume reading in cubic metres (m³).
- Status messages: Shows the PLC's system status, confirmation status, confirmation message, maximum-volume warning, and maintenance message.
- Interface: A simple layout for viewing live data.
- MQTT connection feedback: Shows whether the app is connected and when it last received a message.

The tank is drawn on a 0–1,000,000 m³ scale, with 700,000 m³ as the indicated limit. If the PLC sends a larger value, the numerical reading remains visible while the graphic is capped at full.

System overview
```text
Function generator + sensor (DI2) + button (DI1)
                        |
                        v
           WAGO Compact Controller 100
                        |
                  MQTT publish
                        |
                  MQTT broker
                        |
              CA2 ASP.NET Core app
                        |
              Local dark dashboard
```
              http://localhost:5188
```
