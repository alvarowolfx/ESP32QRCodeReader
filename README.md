# ESP32 QR Code Reader

This library provides a interface to read QR Codes using an ESP32 with a camera.

Internally this lib uses a slight modified version of the [Quirc library](https://github.com/dlbeer/quirc) and some code from OpenMV port on the [MaixPy](https://github.com/sipeed/MaixPy) project.

## Demos

- EN-US - See demo video at https://twitter.com/alvaroviebrantz/status/1290116219199279104?s=20
- PT-BR - Veja video de demo em https://twitter.com/alvaroviebrantz/status/1290116405824806912?s=20

## Usage

Is recommended to use PlatformIO to install this lib. Add `ESP32QRCodeReader` to your `platformio.ini` file.

From PlatformIO library:

```
lib_deps=
  ESP32QRCodeReader
```

Directly from Github:

```
lib_deps =
  alvarowolfx/ESP32QRCodeReader
```

## Examples

This library comes with a number of example sketches. See File > Examples > ESP32QRCodeReader
within the Arduino application. You can see them on the `examples` folder on this repository.

Basic example:

```
#include <ESP32QRCodeReader.h>

// See available models on README.md or ESP32CameraPins.h
ESP32QRCodeReader reader(CAMERA_MODEL_AI_THINKER);

void onQrCodeTask(void *pvParameters)
{
  struct QRCodeData qrCodeData;

  while (true)
  {
    if (reader.receiveQrCode(&qrCodeData, 100))
    {
      Serial.println("Found QRCode");
      if (qrCodeData.valid)
      {
        Serial.print("Payload: ");
        Serial.println((const char *)qrCodeData.payload);
      }
      else
      {
        Serial.print("Invalid: ");
        Serial.println((const char *)qrCodeData.payload);
      }
    }
    vTaskDelay(100 / portTICK_PERIOD_MS);
  }
}

void setup()
{
  Serial.begin(115200);
  Serial.println();

  reader.setup();
  reader.beginOnCore(1);
  xTaskCreate(onQrCodeTask, "onQrCode", 4 * 1024, NULL, 4, NULL);
}
```

## Limitations

- Need an ESP32 module with PSRAM - See below.

## Compatible Hardware

The QR Code recognition lib required a lot of memory, so I was only able to get this to work with an ESP32 that has PSRRAM available and also the Quirc library is modified to use that directly.

- [ESP32](https://espressif.com/en/products/hardware/esp32/overview) module
- PSRAM Available
- Camera module - Tested with OV2640

ESP32 modules with camera that have PSRAM and should work:

- CAMERA_MODEL_WROVER_KIT
- CAMERA_MODEL_ESP_EYE
- CAMERA_MODEL_M5STACK_PSRAM
- CAMERA_MODEL_M5STACK_V2_PSRAM
- CAMERA_MODEL_M5STACK_WIDE
- CAMERA_MODEL_AI_THINKER

ESP32 modules without PSRAM that will not work:

- CAMERA_MODEL_M5STACK_ESP32CAM
- CAMERA_MODEL_TTGO_T_JOURNAL

## License

This code is released under the MIT License.

### References

- https://github.com/dlbeer/quirc
- https://github.com/sipeed/MaixPy
- https://github.com/Schaggo/QR-ARDUINO
- https://github.com/donny681/ESP32_CAMERA_QR
