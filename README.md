<img src="/img/brd.jpg"/>

🤗 Please consider subscribing to my [YouTube channel](https://www.youtube.com/@PricelessToolkit/videos). Your subscription goes a long way in backing my work. If you feel more generous, you can buy me a coffee


[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/U6U2QLAF8)

# TTGO_Gateway
Simple One-Way LoRa Gateway for Home Assistant. The full version with 2-Way communication and ESP-NOW support is [CapiBridge](https://github.com/PricelessToolkit/CapiBridge)

> [!NOTE]
> Preferred board version `TTGO LoRa32 V2.1_1.6.1`

### Links
TTGO LoRa32 V2.1_1.6.1 on Aliexpress [LILYGO® TTGO LoRa32 V2.1_1.6 Version 433/868/915Mhz](https://s.click.aliexpress.com/e/_DdCLj19)


## 📣 Updates, Bugfixes, and Breaking Changes
- 03.02.2026 - WiFi Reconnect.


## Configuration

- In Arduino IDE select Tools > Board > ESP32 Arduino > `ESP32 Dev Module`
- Change the BOARD definition in `board.h` according to your Lilygo gateway HW Version " 1 = ENABLE / 0 = DISABLE ".

```c
#define LORA_V1_0_OLED  0
#define LORA_V1_2_OLED  0
#define LORA_V1_3_OLED  0
#define LORA_V1_6_OLED  0
#define LORA_V2_0_OLED  1
```

### TTGO Boards GPIOs

| Name        | V1.0 | V1.2(T-Fox) | V1.3 | V1.6 | V2.1 |
| ----------- | ---- | ----------- | ---- | ---- | ---- |
| OLED RST    | 16   | N/A         | N/A  | N/A  | N/A  |
| OLED SDA    | 4    | 21          | 4    | 21   | 21   |
| OLED SCL    | 15   | 22          | 15   | 22   | 22   |
| SDCard CS   | N/A  | N/A         | N/A  | 13   | 13   |
| SDCard MOSI | N/A  | N/A         | N/A  | 15   | 15   |
| SDCard MISO | N/A  | N/A         | N/A  | 2    | 2    |
| SDCard SCLK | N/A  | N/A         | N/A  | 14   | 14   |
| DS3231 SDA  | N/A  | 21          | N/A  | N/A  | N/A  |
| DS3231 SCL  | N/A  | 22          | N/A  | N/A  | N/A  |
| LORA MOSI   | 27   | 27          | 27   | 27   | 27   |
| LORA MISO   | 19   | 19          | 19   | 19   | 19   |
| LORA SCLK   | 5    | 5           | 5    | 5    | 5    |
| LORA CS     | 18   | 18          | 18   | 18   | 18   |
| LORA RST    | 14   | 23          | 23   | 23   | 23   |
| LORA DIO0   | 26   | 26          | 26   | 26   | 26   |

## Setting up WIFI and LoRa in the Gateway
- Firmware `TTGO_Gateway.ino`
- If necessary, change the LoRa Configuration in `config.h` config File.
- The LoRa settings in the gateway and in the sensor must match.

```c
/////////////////////////// Gateway Key ///////////////////////////

#define GATEWAY_KEY "xy" // Keep it short

///////////////////////////////////////////////////////////////////////////////

////////////////////////////// WIFI / MQTT ////////////////////////////////////
#define WIFI_SSID "wifi-name"
#define WIFI_PASSWORD "wifi-password"
#define MQTT_USERNAME "mqtt-username"
#define MQTT_PASSWORD "mqtt-password"
#define MQTT_SERVER "IP-Address"
#define MQTT_PORT 1883


////////////////////////// LoRa Config ////////////////////////////////////////

#define SIGNAL_BANDWITH 125E3  // signal bandwidth in Hz, defaults to 125E3
#define SPREADING_FACTOR 8    // ranges from 6-12, default 7 see API docs
#define CODING_RATE 5          // Supported values are between 5 and 8, corresponding to coding rates of 4/5 and 4/8. The coding rate numerator is fixed at 4.
#define SYNC_WORD 0x12         // byte value to use as the sync word, defaults to 0x12
#define PREAMBLE_LENGTH 6      // Supported values are between 6 and 65535.
#define TX_POWER 20            // TX power in dB, defaults to 17, Supported values are 2 to 20
#define BAND 868E6             // 433E6 / 868E6 / 915E6

///////////////////////////////////////////////////////////////////////////////
```

The remaining is to upload the code into the LilyGo board after that, the "LoRaGateway" RSSI entity will appear in the MQTT devices list.


## Protocol "JSON String Sent by a Sensor/Node"

```json
{
  "k": "xy",
  "id": "ESP32",
  "b": "99",
  "rw": "Test123",
  "dr": "on"
}
```
Full Suported MQTT-Autodiscovery List


| Key   | Description               | Unit of Measurement | Required |
|-------|---------------------------|---------------------|----------|
| `k`   | Private Gateway key       |  -                  | Yes      |
| `id`  | Node Name                 |  -                  | Yes      |
| `r`   | RSSI                      | dBm                 | No       |
| `b`   | Battery percent           | %                   | No       |
| `v`   | Voltage                   | Volts               | No       |
| `pw`  | Current                   | mAh                 | No       |
| `l`   | Luminance                 | lux                 | No       |
| `m`   | Motion                    | Binary on/off       | No       |
| `w`   | Weight                    | grams               | No       |
| `s`   | State                     | Anything            | No       |
| `t`   | Temperature               | °C                  | No       |
| `t2`  | Temperature 2             | °C                  | No       |
| `hu`  | Humidity                  | %                   | No       |
| `mo`  | Moisture                  | %                   | No       |
| `rw`  | ROW                       | Anything            | No       |
| `bt`  | Button                    | Binary on/off       | No       |
| `atm` | Pressure                  | kph                 | No       |
| `cd`  | Dioxyde de carbone        | ppm                 | No       |
| `dr`  | Door                      | Binary on/off       | No       |
| `wd`  | Window                    | Binary on/off       | No       |
| `vb`  | Vibration                 | Binary on/off       | No       |


## Sensor / Node Example
The simplest way to create JSON String without the ArduinoJson.h library and transmit it via LoRa. `Example from MailBox sensor`

```c
#define NODE_NAME "mbox"
#define GATEWAY_KEY "xy" // must match CapiBridge's key

float volts = analogReadEnh(PIN_PB4, 12) * (1.1 / 4096) * (30 + 10) / 10;
// Calculate percentage
float percentage = ((volts - 3.2) / (4.2 - 3.2)) * 100;
percentage = constrain(percentage, 0, 100);
int intPercentage = (int)percentage;
	
LoRa.print("{\"k\":\"" + String(GATEWAY_KEY) + "\",\"id\":\"" + String(NODE_NAME) + "\",\"s\":\"mail\",\"b\":" + String(intPercentage) + "}");
```

## Additional links

Official LilyGO Github page : https://github.com/LilyGO/TTGO-LoRa32-V2.1
