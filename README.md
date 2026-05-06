# HA MQTT Problem Sensor — Node-RED Function Node

A reusable Node-RED function node that publishes a Home Assistant **MQTT discovery binary_sensor** using the `problem` device class.

---

## What it does

When triggered with an `"on"` or `"off"` payload, the node emits two MQTT messages:

| # | Topic | Purpose |
|---|-------|---------|
| 0 | `homeassistant/binary_sensor/<UUID>/config` | MQTT discovery config — registers the entity in HA |
| 1 | `homeassistant/binary_sensor/<UUID>/state`  | Current problem state |

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
3. Edit the four constants in the **Node Configuration** section of the function node:

```js
const UUID     = "4bcb696e-9229-4b3f-9b73-08bfeeaaba9e"; // Generate a fresh UUID for each new entity
const NAME     = "Thermostat Problem";                    // Friendly name shown in Home Assistant
const ICON_ON  = "mdi:alert-circle";                      // Icon when problem is active
const ICON_OFF = "mdi:check-circle";                      // Icon when problem is inactive
```

> ⚠️ **Each sensor must have a unique UUID.** Use a generator such as [uuidgenerator.net](https://www.uuidgenerator.net/) when creating additional instances.

---

## Usage

Send a message to the node with:

```js
msg.payload = "on"   // Problem is active
msg.payload = "off"  // Problem is inactive
```

Payload is case-insensitive — `"ON"`, `"On"`, and `"on"` are all accepted.

---

## Flow diagram

```
[Trigger / Sensor] → [mqtt_problem] → [MQTT Out] → Home Assistant
```

---

## Customisation

To create additional problem sensors, import the flow again and update `UUID` and `NAME` (at minimum). Each instance is fully independent.

---

## License

MIT — see [LICENSE](LICENSE).
