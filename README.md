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

## Technologies
- C# / ASP.NET Core (`net10.0`)
- MQTTnet (`5.2.0.1603`)
- HTML, CSS and JavaScript for the dashboard
- Visual Studio 2022 / .NET 10 SDK
No database, Blazor, or MudBlazor is required.

## Run the application
1. Download or clone the repository and open the CA2 Dam Valve Monitor project directory.
2. Open `CA2.slnx` in Visual Studio with the .NET 10 SDK installed.
3. Restore the NuGet packages if Visual Studio asks.
4. Start the `CA2` project, or run `Run_CA2_Dashboard.bat` from the project directory on Windows.
5. Open http://localhost:5188 in a browser.
6. Keep the app running to receive new MQTT messages. Look for `[CONNECTED]` and `MESSAGE #...` in its console window.
Make sure your computer can reach the MQTT broker used by the PLC.

## MQTT data
The app subscribes to:
```text
beaver-iot/mqtt@default/mqtt-device/plc047
```
It reads these JSON fields from the PLC:
|JSON field	| Used for |
| --- | --- |
|`device_id`	| Device identification (`plc047`) |
|`sensor_pnp`	| DI2 sensor lamp: `0` off, `1` triggered |
|`1confirm_button`	| DI1 button lamp: `0` released, `1` pressed |
|`voltage`	| PLC's published analog reading |
|`water_volume_m3`	| Numerical volume and tank level |
|`system_status`	| Overall PLC state (`NORMAL`, `EMERGENCY`, `SENSOR_ERROR`) |
|`confirmation_status`	| Confirmation state (`WAIT_FOR_ACTION`, `CONFIRMED`, `OVERRIDDEN`) |
|`confirmation_message`	| Operator-facing confirmation message |
|`warning_message`	| Maximum-volume warning |
|`maintenance_message`	| Sensor-error maintenance message |
The dashboard displays the readings as published by the PLC. It does not change the PLC's analog scaling or recalculate the water volume. The MQTT payload must be valid, complete JSON for the values to update.

## Dashboard refresh rate
The browser checks for the latest reading every 1 second by default. To change this, edit `CA2/wwwroot/index.html`:
```javascript
refresh();
setInterval(refresh, 1000); // 1000 ms = 1 second
```
For example, use `500` for a half-second dashboard refresh. This does not change how often the PLC sends MQTT messages. The PLC's MQTT publish interval must be configured separately in CODESYS.
