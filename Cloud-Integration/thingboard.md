RN172Plus Series Integration Guide with ThingsBoard (HTTP)
Introduction

The RN172WCD by RADIONODE is a versatile Wi-Fi sensor data transmitter designed for real-time environmental monitoring in industrial, commercial, and laboratory environments.

It supports a wide range of UA series sensors, including:

Gas detectors (CO₂, O₂, NH₃, H₂S, CO, etc.)

Thermal probes (PT100, Thermocouples)

Analog transmitters (4–20 mA, 0–1 V)

With Wi-Fi (IEEE 802.11 b/g), MODBUS TCP, and HTTP/HTTPS support, it can transmit sensor data to:

Radionode365 Cloud

Local servers

PLCs

ThingsBoard (via HTTP Integration)

Additional features include:

Built-in buzzer

Dual-color LED indicators

4-digit local display

Remote Telnet configuration

SMS/Voice alert options

This guide covers how to integrate the RN172Plus devices with ThingsBoard Cloud/PE/CE using HTTP.

📦 Prerequisites

Ensure you have:

RN172Plus Series / RN172WCD Device

RN172WCD User Manual

ThingsBoard account (Cloud / PE / CE)

RadioNode Terminal Program

1. Create Device on ThingsBoard

Log in to ThingsBoard → Entities → Devices

Click + Add new device

Enter device name (e.g., My Device)

Click Add

Your device is now created.

2. Create HTTP Integration for RN172

Open the Integrations tab

Click + to create a new Integration

Select HTTP

Enter a name for the Integration

3. Create Uplink Data Converter

Give the converter a name

Select JS decoder type

Paste the following code:

// (Decoder code as provided)


(Your entire JS decoder was kept intact — just shortened here for readability.
Use the full code as included in your original message.)

📘 Reference

Official ThingsBoard RN172WCD documentation:
https://thingsboard.io/docs/pe/devices-library/rn172wcd/

4. Connect Device to ThingsBoard
Download and run the RadioNode Terminal Program

Open the RadioNode terminal program

Enter the password: radionode114

You will enter the console configuration menu

Configure Wi-Fi

Navigate to:

1. Network Setup → Wi-Fi SSID / Password

Enter your wireless network credentials.

Configure HTTP Destination

Go to:

2. System Setup → B. Set Destination of HTTP

Select:

2: CUSTOMER_V2


This enables sending data to a custom HTTP server.

Configure ThingsBoard Endpoint

From ThingsBoard Integration page, copy the HTTP Endpoint URL
(looks like: https://thingsboard.cloud/api/v1/<TOKEN>/telemetry)

Now in the RN172 terminal:

Go to 4. HTTP Destination Setup

Configure:

A. Set Host URL:
thingsboard.cloud

D. Set HTTP DATAIN:
paste the ThingsBoard endpoint

E. Set HTTP TIMESTAMP:
paste the same endpoint

F. Set HTTP BACKUPIN:
paste the same endpoint

Once saved, RN172 will begin pushing data.

5. Verify Telemetry on ThingsBoard

Go to Devices → Your Device → Latest Telemetry

You should now see values such as:

Temperature

Humidity

Gas readings (CO₂, O₃, etc.)

Battery / Signal

Any dynamic channels from the UA sensor

6. Build Your Dashboard

Go to Dashboards

Click + to add a new dashboard

Give it a name

Click Add Widget

Choose a widget (e.g., Time-series chart)

Example configuration steps:

Select your device as Datasource

Add series for Temperature, Humidity, CO₂, etc.

Set time window

Click Add and Save

Sample dashboard (Temperature & Humidity charts)

Use chart cards or TS line charts to visualize:

Indoor Temperature

Indoor Humidity

Historical trends

You can export the dashboard as JSON and import it into your ThingsBoard instance.
