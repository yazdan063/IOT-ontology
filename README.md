# IoT Smart Home Ontology

A comprehensive OWL ontology for modeling smart home Internet of Things (IoT) devices, sensors, actuators, and their interactions based on the SOSA/SSN (Sensor, Observation, Sample, and Actuator / Semantic Sensor Network) standards.

---

## 🔍 Overview

The **IoT Smart Home Ontology** provides a formal, machine-readable representation of smart home environments, including:

- **Physical structures**: Buildings, floors, rooms, windows, doors
- **IoT devices**: Sensors, actuators, smart appliances
- **Observations**: Time-stamped sensor readings
- **Actuations**: Control commands and device actions
- **Relationships**: Device locations, connectivity, hosting

This ontology follows W3C's **SOSA/SSN** standards for semantic sensor networks, ensuring interoperability and best practices in IoT knowledge representation.

### Use Cases

- Smart home automation systems
- Energy management and monitoring
- Home security systems
- Environmental monitoring
- IoT device integration and discovery
- Semantic reasoning and rule-based automation
- Research in smart buildings and ambient intelligence

---

## ✨ Features

- ✅ **SOSA/SSN Compliant**: Proper integration with W3C standards
- ✅ **Comprehensive Device Taxonomy**: 30+ device types (sensors, actuators, appliances)
- ✅ **Spatial Modeling**: Buildings, floors, rooms, and building components
- ✅ **Temporal Data**: Timestamped observations and actuations
- ✅ **Property Chains**: Automatic location inference through building hierarchy
- ✅ **Rich Metadata**: Manufacturer, model, serial number, IP addresses
- ✅ **Validated**: Tested with HermiT reasoner

---

## 🏗️ Ontology Structure

### Class Hierarchy

```
Device
├── SensingDevice
│   ├── EnvironmentalSensor
│   │   ├── TemperatureSensor
│   │   ├── HumiditySensor
│   │   ├── AirQualitySensor
│   │   ├── LeakDetector
│   │   └── SmokeDetector
│   ├── SecuritySensor
│   │   ├── MotionSensor
│   │   ├── ContactSensor
│   │   ├── Camera
│   │   └── GlassBreakSensor
│   └── EnergySensor
│       ├── PowerSensor
│       └── WaterFlowSensor
├── ActuatingDevice
│   ├── LightingActuator (Switch, Dimmer)
│   ├── ClimateActuator (Thermostat, HVACUnit)
│   └── SecurityActuator (SmartLock, Alarm)
└── CompositeDevice
    └── SmartAppliance
        ├── Refrigerator
        ├── WashingMachine
        ├── Dishwasher
        └── Oven

BuildingComponent
└── Structure
    ├── Building
    ├── Floor
    └── Room (Kitchen, LivingRoom, Bedroom, Bathroom)
└── Aperture (Window, Door)
```

### Key Object Properties

- `deviceLocatedIn`: Links devices to rooms/structures
- `isPartOf`: Building containment hierarchy
- `hasAperture / isApertureOf`: Room-aperture relationships
- `hosts / isHostedBy`: Composite device relationships
- `monitors`: Security sensors → building components
- `controls`: Actuators → controlled entities
- `connectedTo`: Device network connections

### Key Data Properties

- Device Metadata: `hasManufacturer`, `hasModel`, `hasSerialNumber`, `hasIPAddress`
- Device Status: `hasPowerState`, `isOperational`, `hasStatus`
- Observation Data: `observationValue`, `observationUnit`, `observationTimestamp`
- Actuation Data: `actuationCommand`, `actuationTimestamp`

---

## 🚀 Getting Started

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/iot-smart-home-ontology.git
cd iot-smart-home-ontology
```

2. **Load the ontology**
   - **In Protégé**: File → Open → Select `IOT-ontology.ttl`
   - **In Apache Jena**: Use `riot` tool to validate syntax
   ```bash
   riot --validate IOT-ontology.ttl
   ```

3. **Load additional instances** (optional)
```bash
# Merge with critical instances
cat IOT-ontology.ttl critical-objects-instances.ttl > complete-ontology.ttl
```

### Quick Validation

**Verify syntax:**
```bash
rapper -i turtle -c IOT-ontology.ttl
```

**Run reasoner in Protégé:**
1. Open the ontology in Protégé
2. Reasoner → HermiT
3. Start Reasoner
4. Check for inconsistencies

Expected output:
```
INFO  Running Reasoner
INFO  Pre-computing inferences:
      - class hierarchy ✓
      - object property hierarchy ✓
      - data property hierarchy ✓
      - class assertions ✓
      - object property assertions ✓
      - same individuals ✓
INFO  Ontologies processed in ~1200 ms by HermiT
```

---

## 🔎 SPARQL Query Examples

### Setup

Load the ontology into a SPARQL endpoint or use Protégé's SPARQL Query tab.

**Prefix Declarations** (include at the start of each query):
```sparql
PREFIX : <http://www.semanticweb.org/iot-smart-home#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sosa: <http://www.w3.org/ns/sosa/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
```

---

### Query 1: Find All Devices and Their Types

**Purpose**: Retrieve all IoT devices in the smart home with their labels and types.

```sparql
PREFIX : <http://www.semanticweb.org/iot-smart-home#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>

SELECT ?device ?deviceLabel ?type
WHERE {
  ?device rdf:type/rdfs:subClassOf* :Device .
  ?device rdfs:label ?deviceLabel .
  ?device rdf:type ?type .
  FILTER(?type != owl:NamedIndividual)
}
ORDER BY ?deviceLabel
```

---

### Query 2: Find All Devices with Manufacturer and Model

**Purpose**: Retrieve complete device inventory with metadata.

```sparql
PREFIX : <http://www.semanticweb.org/iot-smart-home#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?device ?deviceLabel ?manufacturer ?model ?serialNumber
WHERE {
  ?device rdf:type/rdfs:subClassOf* :Device .
  ?device rdfs:label ?deviceLabel .
  OPTIONAL { ?device :hasManufacturer ?manufacturer }
  OPTIONAL { ?device :hasModel ?model }
  OPTIONAL { ?device :hasSerialNumber ?serialNumber }
}
ORDER BY ?manufacturer
```

---

## 🧠 Reasoning & Validation

### Running the Reasoner

**In Protégé**:
1. Open `IOT-ontology.ttl`
2. Go to: Reasoner → HermiT (or Pellet)
3. Click "Start Reasoner"
4. Wait for completion (~1-2 seconds)
5. Check: Reasoner → Explain Inconsistencies

**Expected Output**:
```
INFO  22:18:12  Running Reasoner
INFO  22:18:12  Pre-computing inferences:
INFO  22:18:12      - class hierarchy ✓
INFO  22:18:12      - object property hierarchy ✓
INFO  22:18:12      - data property hierarchy ✓
INFO  22:18:12      - class assertions ✓
INFO  22:18:12      - object property assertions ✓
INFO  22:18:12      - same individuals ✓
INFO  22:18:13  Ontologies processed in 1185 ms by HermiT
```

### Validation Checklist

- ✅ **No inconsistencies detected**
- ✅ **No unsatisfiable classes**
- ✅ **All individuals properly classified**
- ✅ **Property domains and ranges respected**
- ✅ **Cardinality constraints satisfied**
- ✅ **Disjointness axioms hold**

### Inferred Knowledge

After reasoning, the ontology will infer:
- **Transitive location relationships** (device → room → floor → building)
- **Class memberships** (all parent classes for each instance)
- **Property chain results** (deviceLocatedIn through building hierarchy)
- **Inverse property assertions** (automatic bidirectional relationships)

---

## 📁 Repository Structure

```
iot-smart-home-ontology/
├── README.md                          # This file
├── IOT-ontology.ttl                   # Main ontology file
├── critical-objects-instances.ttl     # Additional test instances
├── sparql-queries-complete.txt        # 50+ SPARQL queries
├── ontology-analysis-report.md        # Detailed analysis
├── docs/
│   ├── class-hierarchy.md             # Class documentation
│   ├── properties.md                  # Property documentation
│   └── use-cases.md                   # Usage examples
├── examples/
│   └── automation-rules.md            # Example automation scenarios
└── tests/
    ├── validation-tests.sparql        # Validation queries
    └── consistency-checks.sparql      # Consistency tests
