# ESP32-Project
Wi-Fi Provisioning Manager Example

This example demonstrates how to use the Wi-Fi Provisioning Manager component to build a provisioning application.

Overview

The example utilizes BLE (Bluetooth Low Energy) as the default transport mode for provisioning communication. It employs NimBLE as the default BLE host but allows switching to Bluedroid via menuconfig.

For ESP32-S2, BLE is unsupported, so SoftAP becomes the default transport mode. On ESP32, you can also configure the transport to use SoftAP from menuconfig.

During provisioning, the device is set up as a Wi-Fi station with specified credentials, which are retained unless a flash erase is performed. Once provisioning completes, BLE is turned off in this example to free memory, though this can be customized.

Key Components

wifi_provisioning: Manages Wi-Fi configuration, data structures, and protocomm endpoint handlers.

protocomm: Handles protocol-based communication and secure session establishment.

protobuf: Serializes data structures for protocomm.

bt: ESP32 BLE stack for transporting protobuf packets.

This example is suitable for adding a provisioning service to any IoT application.

How to Use the Example

Hardware Requirements

This example runs on commonly available ESP32/ESP32-S2 development boards.

Application Requirements

Android

Use the provisioning application available on GitHub: esp-idf-provisioning-android.

iOS

Use the provisioning application available on GitHub: esp-idf-provisioning-ios.

Linux/Windows/macOS

Run the esp_prov.py script found under $IDF_PATH/tools/esp_prov. Ensure dependencies are satisfied before running.

BLE transport is supported only on Linux; for Windows/macOS, it falls back to console mode, requiring an external BLE application.

Configuring the Project

Open idf.py menuconfig.

Set the transport mode under Example Configuration options.

For ESP32-S2, only SoftAP is available.

Building and Flashing the Project

Build the project and flash it to the board:

idf.py -p PORT flash monitor

Monitor the serial output (use Ctrl-] to exit).

Example Output

Upon starting the example, the serial log displays:

I (445) app: Starting provisioning
I (1035) app: Provisioning started
I (1045) wifi_prov_mgr: Provisioning started with service name : PROV_261FCC

Make note of the BLE device name (e.g., PROV_261FCC), which is unique for each device.

To provision the device, run the esp_prov.py script, replacing myssid and mypassword with your Wi-Fi credentials:

python esp_prov.py --transport ble --service_name PROV_261FCC --sec_ver 1 --pop abcd1234 --ssid myssid --passphrase mypassword

Expected output:

I (45345) app: Provisioning successful
I (45345) app: Connected with IP Address:192.168.43.243

Wi-Fi Scanning

Real-time Wi-Fi scan results during provisioning enable the selection of an access point (AP) based on signal strength (RSSI), security type, etc. To use this feature, omit the --ssid and --passphrase parameters:

python esp_prov.py --transport ble --service_name PROV_261FCC --pop abcd1234

Sample output:

==== Wi-Fi Scan results ====
S.N. SSID            CHN  RSSI  AUTH
[1] MyHomeWiFiAP      1   -45   WPA2_PSK
[2] MobileHotspot    11   -46   WPA2_PSK
...

Select an AP and enter its passphrase.

Sending Custom Data

You can send custom data during provisioning by creating and registering additional endpoints:

wifi_prov_mgr_endpoint_create();
wifi_prov_mgr_endpoint_register();

Example command:

python esp_prov.py --custom_data <MyCustomData>

Troubleshooting

Provisioning Failure

If Wi-Fi credentials are incorrect, the monitor log displays:

E (367015) app: Provisioning failed! Reason: Wi-Fi AP password incorrect

Reset the device to factory settings and retry.

Provisioning Not Starting

If the log shows:

I (465) app: Already provisioned, starting Wi-Fi STA

Erase the NVS partition:

$IDF_PATH/components/esptool_py/esptool/esptool.py erase_region 0x9000 0x6000

Unsupported Platform

If esp_prov.py runs in console mode:

BLE client is running in console mode

Follow the interactive prompts for BLE communication manually.

For further assistance, refer to the official ESP-IDF documentation or GitHub repository.


