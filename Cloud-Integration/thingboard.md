# 📡 RN172Plus Series Integration Guide with ThingsBoard (HTTP)

[cite_start]This guide details how to connect the RN-172Plus series device (RN172WCD) to the ThingsBoard IoT Platform using the HTTP protocol[cite: 1, 2].

## 1. Introduction and Prerequisites

[cite_start]The RN172WCD by RADIONODE is a versatile Wi-Fi sensor data transmitter designed for real-time environmental monitoring in industrial, commercial, and laboratory settings[cite: 4]. [cite_start]It supports a wide range of UA series sensors, including gas detectors, thermal sensors, and analog transmitters[cite: 5].

### Prerequisites
* [cite_start]RN-172WCD product [cite: 10]
* [cite_start]UA sensor (Choose from 36 options) [cite: 11]
* [cite_start]Thingsboard account [cite: 12]

## 2. ThingsBoard Setup (UI Configuration)

### 2.1. Create a Device

[cite_start]The device will be provided manually using the ThingsBoard UI[cite: 14].

1.  [cite_start]Login to your ThingsBoard instance and navigate to the **"Entities"** section, then the **"Devices"** page[cite: 15].
2.  [cite_start]Click on the **`+`** icon and then select **"Add new device"**[cite: 16].
3.  Input a device name (e.g., `"RN172"`). [cite_start]No other changes are required[cite: 17].
    ![ThingsBoard Add New Device Screen](images/TB_RN172/TB_2.png)
4.  [cite_start]Click **"Add"** to create the device[cite: 18].

### 2.2. Configure Device Profile

1.  To configure the transport protocol, click the **"Transport configuration"** tab.
2.  [cite_start]Set the **Transport type** to **"Default"**, which supports basic MQTT, HTTP, and CoAP transport[cite: 43, 53].
    ![ThingsBoard Transport Configuration](images/TB_RN172/TB_3.png)
3.  [cite_start]*Optional:* You can set up **Alarm rules** if needed to complete the device setup[cite: 54].
    ![ThingsBoard Alarm Rules Setup](images/TB_RN172/TB_4.png)

### 2.3. Create an HTTP Integration

[cite_start]After adding the device, you need to create an Integration to establish the device connection with the ThingsBoard platform[cite: 80].

1.  [cite_start]Click the **"Integrations"** tab and press the **`+`** sign to start adding a new integration[cite: 81].
    ![ThingsBoard Integrations Tab](images/TB_RN172/TB_5.png)
2.  [cite_start]In **Basic settings**, select the **Integration type** as **"HTTP"**[cite: 114, 132].
    ![ThingsBoard Select Integration Type as HTTP](images/TB_RN172/TB_6.png)
3.  [cite_start]Add a **Name** for the integration and check the boxes for **"Enable integration"** and **"Allow create devices or assets"**[cite: 141, 153, 154].
    ![ThingsBoard Basic Settings for Integration](images/TB_RN172/TB_7.png)

### 2.4. Create an Uplink Data Converter

1.  [cite_start]Click **"Create new"** on the **"Uplink data converter"** tab[cite: 176].
2.  [cite_start]Give the converter a name (e.g., `"Uplink data converter for rn172test"`) and select the decoding configuration type as **JS** (JavaScript)[cite: 159, 172, 177].
3.  Paste the provided comprehensive decoder script (found on pages 10-16 of the guide, handling ATCQ, JSON, and URL-encoded payloads) into the **Main decoding configuration** section.
    ![ThingsBoard Uplink Data Converter Setup](images/TB_RN172/TB_8.png)
4.  [cite_start]Click **"Next"** and then **"Add"** to finalize the integration setup[cite: 157, 211].

### 2.5. Copy the HTTP Endpoint URL

1.  Navigate to the **"Connection"** tab for your integration. [cite_start]The **HTTP endpoint URL** is generated[cite: 205, 206].
2.  [cite_start]**Copy this URL** as you will need it for the device configuration step[cite: 312].
    ![ThingsBoard HTTP Endpoint URL](images/TB_RN172/TB_19.png)

## 3. RN172 Device Configuration

[cite_start]You will use the RadioNode terminal program to configure the RN172 device[cite: 307].

1.  [cite_start]**Access Console:** Run the RadioNode terminal program and enter the password **`radionode114`** to access the console menu[cite: 307].
2.  [cite_start]**Network Setup:** In the network setup, add the Wi-Fi **SSID** and **Password**[cite: 308].
    ![RN172 Terminal WiFi Setup](images/TB_RN172/TB_17.png)
3.  [cite_start]**Set Destination:** Go to the **"2. System Setup"** menu and select **"B. Set Destination of HTTP"**[cite: 309].
4.  Select **"2:CUSTOMER\_V2"**. [cite_start]This means the data will be sent to the custom server V2[cite: 310, 311].
    ![RN172 Terminal HTTP Destination Menu](images/TB_RN172/TB_18.png)
5.  **Set HTTP URL:** Go to the **"4. HTTP Destination Setup"** menu:
    * [cite_start]Set **"A. Set Host URL"** to **`thingsboard.cloud`**[cite: 313].
    * [cite_start]Set **"D. Set HTTP DATAIN"**, **"E. Set HTTP TIMESTAMP"**, and **"F. Set HTTP BACKUPIN"** by pasting the **HTTP endpoint URL** copied from the ThingsBoard integration[cite: 313].
    ![RN172 Terminal Set HTTP Destination URLs](images/TB_RN172/TB_20.png)

## 4. Verification

* Once the device is connected, navigate to the **Devices** page on ThingsBoard.
* [cite_start]Select your device and check the **Latest telemetry** tab to confirm data is being received[cite: 314].
    ![ThingsBoard Latest Telemetry Data](images/TB_RN172/TB_21.png)
* [cite_start]You can then set up a dashboard by selecting options for widgets[cite: 315].

---
