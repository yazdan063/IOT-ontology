# IoT Smart Home Ontology (SOSA/SSN)

A comprehensive OWL ontology for modeling smart home Internet of Things (IoT) environments (devices, sensors, actuators, spaces, events) **based on the W3C SOSA/SSN standards**.

---

## 🔍 Overview

The **IoT Smart Home Ontology** provides a formal, machine-readable representation of a smart home, including:

- **Physical structures**: building, floors, rooms, windows/doors, and outdoor areas
- **IoT devices**: sensors, actuators, smart appliances, and network/edge infrastructure (hub/router/voice assistant)
- **Observations & actuations**: time-stamped sensor readings and control commands (SOSA pattern)
- **Occupants & context**: residents/guests, occupancy, ownership
- **Alerts & notifications**: safety/security events and who gets notified
- **Automation rules**: lightweight representation of rules as individuals (conditions → actions)

This ontology aligns with W3C **SOSA/SSN** for interoperability and supports reasoning (e.g., location inference via property chains and transitive containment).

### Typical Use Cases

- Smart home automation systems (rule-driven control)
- Home security (contact/motion, lock, alerts, notifications)
- Safety monitoring (smoke, leak, CO)
- Energy monitoring and device inventory
- Semantic IoT integration and device discovery
- Research in smart buildings and ambient intelligence

---

## ✨ What’s Included

- ✅ **SOSA/SSN-aligned modeling** (Observation/Actuation patterns)
- ✅ **Rich device taxonomy** (environmental, security, energy + actuators and appliances)
- ✅ **Spatial modeling** (building → floor → room; apertures; outdoor area)
- ✅ **Reasoning-friendly location modeling**
  - `isPartOf` is transitive for containment
  - `deviceLocatedIn` is safe and supports inference via a property chain
- ✅ **Operational metadata** (manufacturer, model, serial, power state, operational status)
- ✅ **Automation + alerting layer**
  - `Alert` (security/fire/leak/CO) + `Notification` + `AutomationRule`

---

## 🏗️ Ontology Structure

### High-level Class Hierarchy (simplified)

```
Device (⊑ ssn:System)
├── SensingDevice (⊑ sosa:Sensor)
│   ├── EnvironmentalSensor
│   │   ├── TemperatureSensor
│   │   ├── HumiditySensor
│   │   ├── AirQualitySensor
│   │   ├── LeakDetector
│   │   ├── SmokeDetector
│   │   └── CarbonMonoxideDetector
│   ├── SecuritySensor
│   │   ├── MotionSensor
│   │   ├── ContactSensor
│   │   ├── Camera
│   │   └── GlassBreakSensor
│   └── EnergySensor
│       ├── PowerSensor
│       ├── WaterFlowSensor
│       └── EnergyMeter
├── ActuatingDevice (⊑ sosa:Actuator)
│   ├── LightingActuator (Switch, Dimmer)
│   ├── ClimateActuator (Thermostat, HVACUnit)
│   ├── SecurityActuator (SmartLock, Alarm)
│   ├── PowerActuator (SmartPlug)
│   ├── ShadingActuator (SmartBlind)
│   └── IrrigationValve
└── CompositeDevice (⊑ sosa:Platform)
    ├── SmartAppliance (Refrigerator, WashingMachine, Dishwasher, Oven)
    └── NetworkDevice (Router, IoTHub, VoiceAssistant)

BuildingComponent (⊑ sosa:FeatureOfInterest)
├── Structure
│   ├── Building
│   ├── Floor
│   ├── Room (Kitchen, LivingRoom, Bedroom, Bathroom, Hallway, Garage)
│   └── OutdoorArea (Garden)
└── Aperture (Window, Door)

Occupant
├── Resident
└── Guest

Alert
├── SecurityAlert
├── FireAlert
├── LeakAlert
└── COAlert

AutomationRule
├── Condition
└── Action
```

### Key Object Properties

**Spatial & containment**
- `isPartOf` (transitive): building component containment (room → floor → building)
- `deviceLocatedIn`: **device → structure** location (safe, with inference via property chain)
- `hasAperture / isApertureOf`: room ↔ window/door

**Device relations**
- `hosts / isHostedBy`: composite device hosting
- `connectedTo`: symmetric device connectivity

**Monitoring / control**
- `monitors`: security sensor → building component
- `controls`: actuator → controlled entity (generic)

**Occupants & context**
- `hasOccupant`: building → occupant
- `occupies`: occupant → structure (current location)
- `ownsDevice`: occupant → device

**Alerts / notifications**
- `triggersAlert`: observation → alert
- `generatesNotification`: alert → notification
- `sentTo`: notification → occupant

**Automation**
- `hasRule`: building → automation rule
- `hasCondition`: rule → condition
- `hasAction`: rule → action
- `targetsDevice`: action → device

### Key Data Properties

**Device metadata**
- `hasManufacturer`, `hasModel`, `hasSerialNumber`
- `hasIPAddress`, `hasMACAddress`

**Device status**
- `hasPowerState` (ON/OFF/STANDBY)
- `isOperational` (boolean)
- `hasStatus` (functional string)

**Observation**
- `observationValue` (subPropertyOf `sosa:hasSimpleResult`)
- `observationUnit`
- `observationTimestamp` (subPropertyOf `sosa:resultTime`)

**Actuation**
- `actuationCommand`
- `actuationTimestamp` (subPropertyOf `sosa:resultTime`)

**Automation**
- `isEnabled`, `rulePriority`
- `comparisonOperator`, `thresholdValue`
- `notificationChannel`

---

## 🚀 Getting Started

### Open in Protégé

1. File → Open → select `IOT-ontology.ttl`
2. Start a reasoner:
   - Reasoner → **HermiT** → Start Reasoner
3. Check:
   - Inferred class hierarchy
   - Inferred `deviceLocatedIn` results (via property chain)

---

## 🔎 SPARQL Query Examples

Use Protégé’s SPARQL tab or any SPARQL endpoint.

**Prefixes**
```sparql
PREFIX : <http://www.semanticweb.org/iot-smart-home#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sosa: <http://www.w3.org/ns/sosa/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
```

### Query 1: Inventory of devices and their locations
```sparql
SELECT ?device ?label ?roomLabel
WHERE {
  ?device rdf:type/rdfs:subClassOf* :Device ;
          rdfs:label ?label .
  OPTIONAL {
    ?device :deviceLocatedIn ?room .
    ?room rdfs:label ?roomLabel .
  }
}
ORDER BY ?label
```

### Query 2: Latest observations (value + unit + timestamp)
```sparql
SELECT ?obs ?label ?value ?unit ?t
WHERE {
  ?obs rdf:type sosa:Observation ;
       rdfs:label ?label ;
       sosa:hasSimpleResult ?value ;
       :observationUnit ?unit ;
       :observationTimestamp ?t .
}
ORDER BY DESC(?t)
```

### Query 3: Alerts and who gets notified
```sparql
SELECT ?alert ?alertLabel ?notif ?channel ?person ?personLabel
WHERE {
  ?alert rdf:type/rdfs:subClassOf* :Alert ;
         rdfs:label ?alertLabel ;
         :generatesNotification ?notif .
  OPTIONAL { ?notif :notificationChannel ?channel }
  ?notif :sentTo ?person .
  OPTIONAL { ?person rdfs:label ?personLabel }
}
ORDER BY ?alertLabel
```

### Query 4: Devices hosted by the IoT hub
```sparql
SELECT ?hubLabel ?deviceLabel
WHERE {
  ?hub rdf:type :IoTHub ; rdfs:label ?hubLabel .
  ?hub :hosts ?d .
  ?d rdfs:label ?deviceLabel .
}
ORDER BY ?deviceLabel
```

---

## 🧠 Reasoning Notes

### Location inference (property chain)
`deviceLocatedIn` supports inference through containment:

- If a device is located in a room
- and that room `isPartOf` a floor/building  
then the reasoner can infer the device is also located in the higher-level structure.

This keeps `isLocatedIn` generic while keeping `deviceLocatedIn` safe (no unintended domain/range side-effects).

---

## 📁 Repository Structure

```
iot-smart-home-ontology/
├── README.md       # Main ontology + instances (v2.3-extended)
├── IOT-ontology.ttl
└── examples/
    └── sparql/                      # Saved SPARQL queries (optional)
```
