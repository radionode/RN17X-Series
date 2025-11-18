# 📡 RN172Plus Series Integration Guide with ThingsBoard (HTTP)

This guide details how to connect the RN-172Plus series device (RN172WCD) to the ThingsBoard IoT Platform using the HTTP protocol.

## 1. Introduction and Prerequisites

The RN172WCD by RADIONODE is a versatile Wi-Fi sensor data transmitter designed for real-time environmental monitoring. It supports a wide range of UA series sensors (gas detectors, thermal sensors, and analog transmitters) and provides connectivity via Wi-Fi, MODBUS TCP, and HTTP/HTTPS.

### Prerequisites
* **Device:** RN-172WCD product
* **Sensor:** UA sensor (Choose from 36 options)
* **Platform:** Thingsboard account

---

## 2. ThingsBoard Setup (UI Configuration)

### 2.1. Create a Device

1.  Login to your ThingsBoard instance and navigate to the **"Entities"** section, then the **"Devices"** page.
2.  Click on the **`+`** icon and then select **"Add new device"**.
3.  Input a device name (e.g., `"RN172"`).
    ![ThingsBoard Add New Device Screen](images/TB_RN172/TB_2.png)
4.  Click **"Add"**.

### 2.2. Configure Device Profile

1.  Click the **"Transport configuration"** tab.
2.  Set the **Transport type** to **"Default"**, which supports MQTT, HTTP, and CoAP transport.
    ![ThingsBoard Transport Configuration](images/TB_RN172/TB_3.png)
3.  *Optional:* You can set up **Alarm rules** if needed.
    ![ThingsBoard Alarm Rules Setup](images/TB_RN172/TB_4.png)

### 2.3. Create an HTTP Integration

1.  Click the **"Integrations"** tab and press the **`+`** sign.
    ![ThingsBoard Integrations Tab](images/TB_RN172/TB_5.png)
2.  In **Basic settings**, select the **Integration type** as **"HTTP"**.
    ![ThingsBoard Select Integration Type as HTTP](images/TB_RN172/TB_6.png)
3.  Add a **Name** for the integration and check the boxes for **"Enable integration"** and **"Allow create devices or assets"**.
    ![ThingsBoard Basic Settings for Integration](images/TB_RN172/TB_7.png)

### 2.4. Create an Uplink Data Converter

1.  Click **"Create new"** on the **"Uplink data converter"** tab.
2.  Name the converter and select the decoding configuration type as **JS** (JavaScript).
3.  **Paste the full decoder script** (found on pages 10-16 of the original guide) into the **Main decoding configuration** section. This script handles ATCQ, JSON, and URL-encoded payloads.
    ![ThingsBoard Uplink Data Converter Setup](images/TB_RN172/TB_8.png)
4.  Click **"Next"** and then **"Add"**.

### 2.5. Copy the HTTP Endpoint URL

1.  Navigate to the **"Connection"** tab for your integration. The **HTTP endpoint URL** is generated.
2.  **Copy this URL** for the device configuration step.
    ![ThingsBoard HTTP Endpoint URL](images/TB_RN172/TB_19.png)

---

## 3. RN172 Device Configuration

You will use the RadioNode terminal program to configure the RN172 device.

1.  **Access Console:** Run the RadioNode terminal program and enter the password **`radionode114`** to access the console menu.
2.  **Network Setup:** Add the Wi-Fi **SSID** and **Password**.
    ![RN172 Terminal WiFi Setup](images/TB_RN172/TB_17.png)
3.  **Set Destination:** Go to the **"2. System Setup"** menu and select **"B. Set Destination of HTTP"**.
4.  Select **"2:CUSTOMER\_V2"** to send data to the custom server V2.
    ![RN172 Terminal HTTP Destination Menu](images/TB_RN172/TB_18.png)
5.  **Set HTTP URL:** Go to the **"4. HTTP Destination Setup"** menu:
    * Set **"A. Set Host URL"** to **`thingsboard.cloud`**.
    * Set **"D. Set HTTP DATAIN"**, **"E. Set HTTP TIMESTAMP"**, and **"F. Set HTTP BACKUPIN"** by pasting the **HTTP endpoint URL** copied from the ThingsBoard integration.
    ![RN172 Terminal Set HTTP Destination URLs](images/TB_RN172/TB_20.png)

---

## 4. Verification

* Once the device is connected, navigate to the **Devices** page on ThingsBoard.
* Select your device and check the **Latest telemetry** tab to confirm data is being received.
    ![ThingsBoard Latest Telemetry Data](images/TB_RN172/TB_21.png)
* You can then proceed to set up a dashboard.

---
