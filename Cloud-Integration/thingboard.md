#  RN172Plus Series Integration Guide with ThingsBoard (HTTP)

This guide details how to connect the **RN172plus series** device (RN172WCD) to the ThingsBoard platform using the HTTP protocol, based on official documentation.

## Introduction

The RN172WCD by RADIONODE is a versatile Wi-Fi sensor data transmitter designed for real-time environmental monitoring. It supports a wide range of UA series sensors and offers Wi-Fi (IEEE 802.11 b/g), MODBUS TCP, and HTTP/HTTPS connectivity.

### Prerequisites

To continue with this guide, you will need the following:
* **Device:** RN172plus series (RN172WCD)
* RN172WCD user manual
* ThingsBoard account

---

## 1. Create Device on ThingsBoard

For simplicity, we will create the device manually using the UI.

1.  Log in to your ThingsBoard instance and navigate to **Entities**, then open the **Devices** page.
2.  Click the **`+`** icon in the top right corner of the table and select **"Add new device"**.
3.  Enter a device name (e.g., `My RN172 Device`). No other changes are required at this stage.
4.  Click **"Add"** to create the device.

---

## 2. Install Required Payload Decoders (Create Integration)

After adding the device, we need to create an Integration for establishing the device connection with the ThingsBoard platform.

1.  Click the **Integrations** tab and press the **`+`** sign to start adding an integration.
    ![ThingsBoard Integrations Tab](images/TB_RN172/TB_5.png)
2.  Select **"HTTP"** as the **Integration type** for the RN172 device.
    ![ThingsBoard Select Integration Type as HTTP](images/TB_RN172/TB_6.png)
3.  Add a **Name** for the integration (e.g., `RN172_HTTP`).
    ![ThingsBoard Integration Basic Settings](images/TB_RN172/TB_7.png)

### 2.1. Create Uplink Data Converter

1.  The next step is to create a data converter for our device. Click **"Create new"** on the Uplink data converter section.
2.  Give a name to the data converter (e.g., `RN172_Decoder`).
3.  Set the decoding configuration type to **JS** (JavaScript) and **paste the provided comprehensive decoder script** into the code box. This script handles the various RN172 payload formats.
    ![ThingsBoard Uplink Data Converter Setup](images/TB_RN172/TB_8.png)
    > **Note:** The full JS code is required here to correctly parse the telemetry and attributes.
4.  After setting up the converter, complete the integration setup.

### 2.2. Copy HTTP Endpoint URL

1.  From the **Integration** tab in ThingsBoard, locate and copy the **HTTP endpoint URL**. This URL is required for setting up the device terminal.
    ![ThingsBoard HTTP Endpoint URL](images/TB_RN172/TB_19.png)

---

## 3. Connect Device to ThingsBoard (Terminal Configuration)

You will use the RadioNode terminal program to configure the device's connection settings.

1.  Download and run the RadioNode terminal program.
2.  Enter the password **`radionode114`** and enter the console menu.
3.  In the **network setup**, add the Wi-Fi **SSID** and **Password**.
    ![RN172 Terminal WiFi Setup](images/TB_RN172/TB_17.png)
4.  Go to the **“2. System Setup”** menu and select **“B. Set Destination of HTTP”**.
5.  Select **“2:CUSTOMER\_V2”** from the destinations. This ensures data is sent to the custom server V2.
    ![RN172 Terminal HTTP Destination Menu](images/TB_RN172/TB_18.png)
6.  Go to the **“4. HTTP Destination Setup”** menu:
    * Select **“A. Set Host URL”** and set it to: `thingsboard.cloud`
    * Select **“D. Set HTTP DATAIN”**, **“E. Set HTTP TIMESTAMP”**, and **“F. Set HTTP BACKUPIN”**. For all three, **paste the HTTP endpoint URL** copied from the ThingsBoard integration.
    ![RN172 Terminal Set HTTP Destination URLs](images/TB_RN172/TB_20.png)

---

## 4. Verification and Dashboard Setup

### 4.1. Check Data on ThingsBoard

1.  Once the device is connected and transmitting, navigate to the **Devices** page.
2.  Select your device and check the **Latest telemetry** tab to confirm data is being received correctly.
    ![ThingsBoard Latest Telemetry Data](images/TB_RN172/TB_21.png)

### 4.2. Setting up the Dashboard

1.  Go to the **Dashboards** page and press the **`+`** button to create a new Dashboard.
2.  Enter a name for the Dashboard and then click **Add**.
3.  After creation, the dashboard will open automatically, and you can start adding widgets. Click **"Add widget"**.
4.  Select a widget (e.g., a **Time series chart** from the Charts bundle) to visualize your device data.
5.  In the widget settings, define the display time window and specify your device in the **Datasource** field. Click **Add**.

---
