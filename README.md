# XIAO ESP32S3 BLE Stepper Controller

ESP32をBLEペリフェラルにして、Web Bluetooth対応ブラウザからA4988経由のステップモータを制御するサンプルです。

## 構成

- `src/main.cpp`: XIAO ESP32S3向けPlatformIOファームウェア
- `web/index.html`: GitHub PagesなどHTTPS上で動くWeb Bluetoothアプリ
- `index.html`: GitHub Pagesのルートから`web/`へ移動するための入口

## BLE仕様

Device name:

- XIAO ESP32S3: `XIAO BLE Motor`
- ESP32 DevKit: `ESP32 BLE Motor`

Service UUID: `7b7f0001-9b6d-4f8b-8c5d-9bb6f6f68c01`

Characteristics:

- Command Write: `7b7f0002-9b6d-4f8b-8c5d-9bb6f6f68c01`
- Status Notify: `7b7f0003-9b6d-4f8b-8c5d-9bb6f6f68c01`

コマンドはASCIIテキストです。

```text
C,<microstep>,<d_us>,<steps>,<accel>
V,<signed_speed_hz>,<accel>
M,<signed_steps>
S
E,<0|1>
R
```

`V`はジョグ/速度指令です。正値がCW、負値がCCW、0が減速停止です。

## Web Bluetooth

Web BluetoothはHTTPSまたはlocalhost上でのみ動作します。GitHub Pagesに公開したURLへChromeまたはEdgeでアクセスしてください。iOS SafariはWeb Bluetoothに対応していません。

Webアプリの速度入力は `steps/s` に統一しています。ファームウェア内部の `d_us` は、Webアプリ側で `d_us = 500000 / steps_per_second` として自動換算して送信します。

## ビルド

```powershell
platformio run -e seeed_xiao_esp32s3
platformio run -e esp32dev
```

書き込み:

```powershell
platformio run -e seeed_xiao_esp32s3 --target upload
platformio run -e esp32dev --target upload
```

## ピン割り当て

| Signal | XIAO ESP32S3 | ESP32 DevKit |
| --- | --- | --- |
| DIR | D8 | GPIO26 |
| STEP | D9 | GPIO25 |
| MS1 | D0 | GPIO14 |
| MS2 | D1 | GPIO27 |
| MS3 | D2 | GPIO33 |
| EN | D3 | GPIO32 |

ESP32 DevKitで別のGPIOを使う場合は、`platformio.ini` の `[env:esp32dev]` の `MOTOR_PIN_*` を変更してください。
