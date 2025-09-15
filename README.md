# ESP8266 Smart Home Controller with Sinric Pro

![Platform](https://img.shields.io/badge/Platform-ESP8266-purple.svg) ![Framework](https://img.shields.io/badge/Framework-Arduino-00979D.svg) ![Cloud](https://img.shields.io/badge/IoT%20Cloud-Sinric%20Pro-F5A300.svg)

This project transforms a standard ESP8266 board into a powerful, multi-channel smart switch. It allows you to control home appliances using voice commands via **Amazon Alexa** or **Google Home**, the **Sinric Pro app**, and physical push buttons. The system ensures that the state of your devices is always synchronized between the app and manual controls.



---

## 📋 Features

* **Voice Control**: Natively integrates with Amazon Alexa and Google Home through the Sinric Pro service.
* **Manual Override**: Supports physical push buttons (or toggle switches) for manual control of each appliance.
* **State Synchronization**: Real-time updates are sent to the Sinric Pro cloud when a device is toggled manually, keeping your app and voice assistants perfectly in sync.
* **Multi-Channel**: Easily configurable to control multiple relays (up to 4 in the default code).
* **Status Indicator**: An onboard LED indicates the Wi-Fi connection status.
* **Reliable & Configurable**: Uses a C++ `map` for a clean and scalable way to manage devices and their corresponding GPIO pins.

---

## 🛠️ Hardware & Software Requirements

### Hardware
* **Microcontroller**: An ESP8266-based board (e.g., NodeMCU, Wemos D1 Mini).
* **Relay Module**: A 5V multi-channel relay module (e.g., 4-channel).
* **Switches**: Tactile push buttons (momentary) or physical toggle switches.
* **Indicator**: A standard LED for Wi-Fi status.
* **Power Supply**: A 5V power supply capable of powering the ESP8266 and the relay module.
* **Connecting Wires**.

### Software
* **[Arduino IDE](https://www.arduino.cc/en/software)**
* **ESP8266 Board Support Package** for Arduino.
* **[Sinric Pro Library](https://github.com/sinricpro/esp8266-esp32-sdk)**: Can be installed from the Arduino Library Manager.
* **A free [Sinric Pro](https://sinric.pro/) account**.

---

## ⚙️ How It Works

The system operates by connecting to the Sinric Pro IoT cloud, which acts as a bridge between the ESP8266 and smart home services like Alexa.

1.  **Configuration**: At the start of the code, you define your Wi-Fi credentials, Sinric Pro App Key/Secret, and the unique IDs for each device you want to control. You also map these device IDs to the specific GPIO pins connected to your relays and physical switches.

2.  **Connection**: On boot, the ESP8266 connects to your local Wi-Fi network. Once connected, it establishes a persistent WebSocket connection to the Sinric Pro servers using your credentials.

3.  **Two Modes of Control**:
    * **Cloud Command (Voice/App)**: When you issue a command like "Alexa, turn on the light," the command goes to the Sinric Pro cloud. Sinric Pro sends a message to the correct ESP8266 device. The `onPowerState` callback function in the code is triggered, which finds the appropriate relay pin and toggles its state (ON/OFF).
    * **Manual Command (Physical Switch)**: The `loop()` function constantly calls `handleFlipSwitches()`, which scans the GPIO pins for any physical button presses. To prevent errors from electrical noise, it includes a **debounce** mechanism. When a valid press is detected, it toggles the relay's state directly.

4.  **State Synchronization**: This is the most critical part. After a physical switch is used, the code immediately sends an event (`sendPowerStateEvent`) back to the Sinric Pro cloud. This updates the device's status on the server, ensuring that your Alexa and mobile apps always show the correct, real-time state of the device.

---

## 🚀 Setup and Installation

### 1. Sinric Pro Setup
* Create a free account at **[sinric.pro](https://sinric.pro/)**.
* Go to the **Devices** menu and click **Add Device**.
* Create a new device of type **Switch**. Give it a name (e.g., "Living Room Light"). Repeat for as many devices as you need.
* Go to the **Credentials** menu and copy your **App Key**, **App Secret**, and the **Device IDs** for the switches you just created.

### 2. Arduino IDE Setup
* Make sure you have the **ESP8266 board manager** installed in your Arduino IDE.
* Go to **Sketch > Include Library > Manage Libraries...** and search for `SinricPro`. Install the library by Boris Jaeger.
* Copy the entire source code from this project into a new Arduino sketch.

### 3. Code Configuration
* Paste your **Wi-Fi credentials**, **Sinric Pro credentials**, and **Device IDs** into the `#define` section at the top of the code.
* Adjust the `RelayPin` and `SwitchPin` GPIO numbers if your hardware wiring is different.

### 4. Hardware Assembly
* Connect the relays, physical switches, and status LED to the GPIO pins on the ESP8266 as defined in the code.
* **Important**: Ensure you have a common ground (`GND`) between the ESP8266, the relay module, and the power supply.

### 5. Upload
* Select your ESP8266 board from the **Tools > Board** menu in the Arduino IDE.
* Choose the correct COM port.
* Click **Upload** to flash the firmware to your device. You can monitor the connection progress in the Serial Monitor.

Once running, you can link the Sinric Pro skill in your Amazon Alexa or Google Home app to start using voice commands!
