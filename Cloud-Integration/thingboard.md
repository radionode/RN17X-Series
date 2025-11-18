# 📡 RN172Plus Series Integration Guide with ThingsBoard (HTTP)

## Introduction

The **RN172WCD** by RADIONODE is a versatile Wi-Fi transmitter for real-time environmental and gas monitoring in industrial, commercial, and laboratory environments.

It supports a wide range of UA Series sensors:

* Gas sensors (CO₂, O₂, NH₃, H₂S, CO, etc.)
* Thermal sensors (PT100, Thermocouples)
* Analog inputs (4–20 mA, 0–1 V)

It communicates via:

* Wi-Fi (IEEE 802.11 b/g)
* MODBUS TCP
* HTTP / HTTPS

The device can send data to:

* Radionode365 Cloud
* Local servers
* PLC systems
* ThingsBoard (this guide)

Additional features:

* Built-in buzzer
* Multi-color LED indicators
* 4-digit display
* Remote Telnet configuration
* Alarm notifications (SMS, voice)

---

## 📦 Prerequisites

To continue with this guide, you will need:

* RN172Plus series device
* RN172WCD user manual
* ThingsBoard account (Cloud / CE / PE)

---

## 1. Create Device on ThingsBoard

1.  Log in and navigate to **Entities** → **Devices**
2.  Click **`+` Add new device**
3.  Enter a device name (e.g., `My Device`)
4.  Click **Add**

Your device has been successfully added.
![ThingsBoard Add New Device Screen](images/TB_RN172/TB_2.png)

---

## 2. Install Required Payload Decoders

After adding the device, you must create an HTTP Integration so the RN172 can send data to ThingsBoard.

1.  Click **Integrations**
2.  Click the **`+`** button to add a new integration
    ![ThingsBoard Integrations Tab](images/TB_RN172/TB_5.png)

3.  Select **HTTP**
    ![ThingsBoard Select Integration Type as HTTP](images/TB_RN172/TB_6.png)

4.  Enter a name for the integration
    ![ThingsBoard Integration Basic Settings](images/TB_RN172/TB_7.png)

Next: create a Data Converter.

1.  Name your converter
2.  Select Decoder Type: **JS**
3.  Paste the following code:
    ![ThingsBoard Uplink Data Converter Setup](images/TB_RN172/TB_8.png)

### 🧩 Full ThingsBoard Decoder (Copy & Paste)

```javascript
/**
 * Helper function to decode raw payload bytes to a string using a simple loop.
 * This method is more robust and compatible with older JS environments.
 */function decodeToString(payload) {
    var text = "";
    var i = 0;
    while (i < payload.length) {
        text += String.fromCharCode(payload[i]);
        i++;
    }
    return text;
}/**
 * Helper function to decode raw payload bytes to a JSON object.
 * It safely parses the string and returns a JSON object.
 */function decodeToJson(payload) {
    try {
        var str = decodeToString(payload);
        return JSON.parse(str);
    } catch (e) {
        return null;
    }
}/**
 * A custom parser to handle URL-encoded payloads.
 */function parseUrlEncoded(payload) {
    var data = {};
    var bodyStr = decodeToString(payload).trim();
    if (bodyStr.indexOf('=') !== -1) {
        var pairs = bodyStr.split("&");
        var i = 0;
        while (i < pairs.length) {
            var parts = pairs[i].split("=");
            if (parts.length === 2) {
                var key = decodeURIComponent(parts[0].trim());
                var value = decodeURIComponent((parts[1] || "").trim());
                data[key] = value;
            }
            i++;
        }
    }
    return data;
}// --- Main ThingsBoard Decode Function ---function decodePayload(payload, metadata) {
    var deviceName = "Unknown_Device";
    var attributes = {};
    var telemetry = {};
    var telemetryTimestamp = null;
    var data = null;
    var detectedModel = null;

    // --- Model Lookup Table ---
    const ua_models_js = {
        "UA58-KFG": ["CO", "O2", "H2S", "CO2"],
        "UA58-CO2": ["CO2", "TEMP", "RH"],
        "UA58-DFG": ["CO", "CH2O", "C6H6"],
        "UA58-LEL": ["LEL", "TEMP", "RH", "CLAS"],
        "UA58-APC": ["CO2", "O3", "TEMP", "RH"],
        "UA50": ["TVOC", "ECO2"],
        "UA52-CO2": ["CO2", "TEMP"],
        "UA52-O2": ["O2", "TEMP"],
        "UA53-CO": ["CO", "TEMP", "RH"],
        "UA53-H2S": ["H2S", "TEMP", "RH"],
        "UA53-O3": ["O3", "TEMP", "RH"],
        "UA53-SO2": ["SO2", "TEMP", "RH"],
        "UA53-NO2": ["NO2", "TEMP", "RH"],
        "UA54-NH3": ["NH3", "TEMP", "RH"],
        "UA54-H2S": ["H2S", "TEMP", "RH"],
        "UA54-C2H4": ["C2H4", "TEMP", "RH"],
        "UA54-EO": ["EO", "TEMP", "RH"],
        "UA54-H2": ["H2", "TEMP", "RH"],
        "UA54-HCL": ["HCL", "TEMP", "RH"],
        "UA54-NO": ["NO", "TEMP", "RH"],
        "UA54-CL2": ["CL2", "TEMP", "RH"],
        "UA54-O2": ["O2", "TEMP", "RH"],
        "UA54-VOC": ["VOC", "TEMP", "RH"],
        "UA10": ["TEMP", "RH"],
        "UA11": ["TEMP", "TEMP"],
        "UA12": ["TEMP", "TEMP"],
        "UA13": ["TEMP"],
        "UA20A": ["mA", "mA"],
        "UA20B": ["mA"],
        "UA20C": ["mV", "mV"],
        "UA20D": ["Puls"],
        "UA20E": ["CH1", "CH2", "CH3", "CH4", "CH5", "CH6"],
        "UA59-CO2": ["CO2", "TEMP"],
        "UA60-PMVT": ["PM25", "PM10", "PM01", "TVOC", "TEMP", "RH"],
        "UA-DEVICE": ["TEMP", "RH"],
        "RN400-PG": [],
        "RN172WC": []
    };

    // --- Step 1: Payload Parsing ---
    var inputString = decodeToString(payload).trim();
    var atcqMatch = inputString.match(/ATCQ\s+([^,]+),([^,]+),([^,]+),([^,]+)/);
    
    if (atcqMatch) {
        data = {};
        data.co2 = parseFloat(atcqMatch[1]);
        data.o3 = parseFloat(atcqMatch[2]);
        data.temperature = parseFloat(atcqMatch[3]);
        data.humidity = parseFloat(atcqMatch[4]);
        detectedModel = 'UA58-APC';
    } else {
        data = decodeToJson(payload);
        if (!data) {
            data = parseUrlEncoded(payload);
        }
    }

    if (!data || Object.keys(data).length === 0) {
        return null;
    }

    var originalSModel = data.model || data.SMODEL;
    detectedModel = detectedModel || originalSModel;

    if (detectedModel === 'UA-DEVICE' && data.C000) {
        var channelData = data.C000.split('|').filter(x => x.trim() !== '');
        if (channelData.length === 5) {
            detectedModel = 'UA58-APC';
        }
    }

    var modelVars = ua_models_js[detectedModel];

    if (data.mac) {
        deviceName = data.mac;
        attributes.mac = data.mac;
    }
    if (data.ver) attributes.firmware = data.ver;
    if (originalSModel) attributes.SMODEL = originalSModel;
    if (data.model) attributes.model = data.model;
    if (detectedModel) attributes.detected_model = detectedModel;
    if (data.ip) attributes.ip = data.ip;
    if (data.splrate) attributes.samplingRate = parseFloat(data.splrate);
    if (data.rsti) attributes.rsti = parseFloat(data.rsti);
    if (data.interval) attributes.transmitInterval = parseFloat(data.interval);
    if (data.tags) attributes.tags = data.tags;

    if (detectedModel && modelVars) {
        var dynamicKey;
        for (dynamicKey in data) {
            if (dynamicKey.match(/^(C|P|CH)\d+/)) {
                var channelData = data[dynamicKey].split('|').filter(x => x.trim() !== '');
                if (channelData.length > 1) {
                    telemetryTimestamp = parseInt(channelData[0]) * 1000;
                    var i = 0;
                    while (i < modelVars.length) {
                        var varName = modelVars[i];
                        if (varName !== '----') {
                            var value = parseFloat(channelData[i + 1]);
                            if (!isNaN(value)) {
                                telemetry[varName.toLowerCase()] = value;
                            }
                        }
                        i++;
                    }
                }
            }
        }
    }

    if (data.co2 !== undefined) telemetry.co2 = parseFloat(data.co2);
    if (data.o3 !== undefined) telemetry.o3 = parseFloat(data.o3);
    if (data.temperature !== undefined) telemetry.temperature = parseFloat(data.temperature);
    if (data.humidity !== undefined) telemetry.humidity = parseFloat(data.humidity);
    if (data.sig !== undefined) telemetry.signal = parseFloat(data.sig);
    if (data.bat !== undefined) telemetry.battery = parseFloat(data.bat);
    if (data.analog_1 !== undefined) telemetry.analog_1 = parseFloat(data.analog_1);
    if (data.analog_2 !== undefined) telemetry.analog_2 = parseFloat(data.analog_2);

    if (detectedModel === "RN172WC" && data.tags) {
        var tagValues = data.tags.split('|').filter(s => s.trim() !== '');
        if (tagValues.length >= 2) {
            telemetry.temperature = parseFloat(tagValues[0].trim());
            telemetry.humidity = parseFloat(tagValues[1].trim());
        }
    }

    if (Object.keys(telemetry).length === 0) {
        telemetry.heartbeat = Date.now();
    }
    
    var result = {
        deviceName: deviceName,
        telemetry: {}
    };

    var finalTelemetryTs = telemetryTimestamp || Date.now();
    for (var key in telemetry) {
        result.telemetry[key] = telemetry[key];
    }

    for (var key in attributes) {
        result.telemetry[key] = attributes[key];
    }
    
    return result;
}return decodePayload(payload, metadata);
