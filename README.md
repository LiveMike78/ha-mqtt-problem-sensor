# HA MQTT Problem Sensor — Node-RED Function Node

A reusable Node-RED function node that publishes a Home Assistant **MQTT discovery binary_sensor** using the `problem` device class. Optionally exposes a single state attribute (e.g. a list of affected devices).

---

## What it does

When triggered, the node emits two MQTT messages:

| # | Topic | Purpose |
|---|-------|---------|
| 0 | `homeassistant/binary_sensor/<UUID>/config` | MQTT discovery config — registers the entity in HA |
| 1 | `homeassistant/binary_sensor/<UUID>/state`  | Current problem state (and attribute, if configured) |

The node status badge in Node-RED is updated on every message, showing the current state and timestamp.

---

## Requirements

- **Node-RED** (any recent version)
- **Home Assistant** with the MQTT integration enabled
- An **MQTT broker** shared between Node-RED and Home Assistant (e.g. Mosquitto)

---

## Installation

1. In Node-RED, open **Menu → Import** and paste (or upload) `ha_mqtt_problem_sensor.json`.
2. Wire the output to an **MQTT Out** node pointed at your shared broker.
3. Edit the constants in the **Node Configuration** section of the function node:

```js
const UUID          = "a3ad6610-529b-403b-be6b-c6ab54a2bc9f"; // Generate a fresh UUID for each new entity
const NAME          = "Low Battery Problem";                   // Friendly name shown in Home Assistant
const ICON_ON       = "mdi:battery-alert";                     // Icon when problem is active
const ICON_OFF      = "mdi:check-circle";                      // Icon when problem is inactive
const ATTRIBUTE_KEY = "devices";                               // Set to "" or null if no attribute is needed
```

> ⚠️ **Each sensor must have a unique UUID.** Use a generator such as [uuidgenerator.net](https://www.uuidgenerator.net/) when creating additional instances.

---

## Usage

### Without an attribute (`ATTRIBUTE_KEY = ""`)

Send a plain string:

```js
msg.payload = "on"   // Problem is active
msg.payload = "off"  // Problem is inactive
```

### With an attribute (`ATTRIBUTE_KEY = "devices"`)

Send an object whose key matches `ATTRIBUTE_KEY` exactly:

```js
msg.payload = {
    state: "on",
    devices: ["Hallway sensor", "Bedroom remote"]
}
```

The attribute value can be any JSON-serialisable type — a list, string, number, or object.

Payload state values are case-insensitive — `"ON"`, `"On"`, and `"on"` are all accepted.

---

## Flow diagram

```
[Trigger / Sensor] → [mqtt_problem] → [MQTT Out] → Home Assistant
```

---

## Customisation

To create additional problem sensors, import the flow again and update `UUID` and `NAME` (at minimum). Each instance is fully independent.

### Example configurations

| Sensor | `NAME` | `ICON_ON` | `ATTRIBUTE_KEY` |
|--------|--------|-----------|-----------------|
| Low battery | `"Low Battery Problem"` | `mdi:battery-alert` | `"devices"` |
| Thermostat fault | `"Thermostat Problem"` | `mdi:alert-circle` | `""` |
| Door left open | `"Door Problem"` | `mdi:door-open` | `"affected_doors"` |

---

## Changelog

### 1.1
- Added optional `ATTRIBUTE_KEY` constant to expose a single HA state attribute.
- `msg.payload` now accepts either a plain `"on"`/`"off"` string or an object `{ state, <attr> }`.
- Attribute discovery fields (`json_attributes_topic`, `json_attributes_template`) are only included in the config payload when `ATTRIBUTE_KEY` is set.

### 1.0
- Initial release.

---

## License

MIT — see [LICENSE](LICENSE).
