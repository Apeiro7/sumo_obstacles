# SUMO Obstacle Simulation

[![SUMO](https://img.shields.io/badge/SUMO-1.19.0-blue.svg)](https://sumo.dlr.de/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Developer](https://img.shields.io/badge/Developer-Amit_Bhardwaj-blue.svg)](LICENSE)
#readme-content img {
  max-width: 100%;
  height: auto;
}

#readme-content p img[src*="shields.io"] {
  display: inline;
  margin: 0;
  vertical-align: middle;
}

A SUMO (Simulation of Urban Mobility) simulation demonstrating lane-changing behavior when vehicles encounter a static obstacle.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [File Structure](#file-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Customization](#customization)
- [Analysis](#analysis)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This simulation creates a realistic traffic scenario where moving vehicles must change lanes to avoid a stationary obstacle blocking one lane of a two-lane road. It's designed for testing lane-changing algorithms, analyzing traffic flow disruption, and educational purposes.

## ✨ Features

- 🚗 Realistic vehicle dynamics and lane-changing behavior
- 🚧 Static obstacle simulation
- 🛣️ Two-lane road network (500m length)
- 🚫 Disabled teleportation for authentic traffic flow
- 📊 Configurable vehicle parameters
- 🔧 Easy customization and extension

## 📁 File Structure

```
.
├── obstacle.nod.xml     # Network nodes definition
├── obstacle.edg.xml     # Road edges definition  
├── obstacle.net.xml     # Compiled network file
├── obstacle.rou.xml     # Routes and vehicle definitions
└── obstacle.sumocfg     # Main simulation configuration
```

### File Descriptions

#### `obstacle.nod.xml` - Node Definition
```xml
<nodes>
    <node id="start" x="0" y="0" type="priority"/>
    <node id="end" x="500" y="0" type="priority"/>
</nodes>
```
Defines network nodes (junctions):
- **start**: Origin point at (0, 0)
- **end**: Destination at (500, 0)

#### `obstacle.edg.xml` - Edge Definition  
```xml
<edges>
    <edge id="edge0" from="start" to="end" priority="1" numLanes="2" speed="13.9"/>
</edges>
```
Road structure configuration:
- 2 lanes, 500m length
- Speed limit: 13.9 m/s (~50 km/h)

#### `obstacle.net.xml` - Network File
Auto-generated network file containing:
- Lane geometry and coordinates
- Junction definitions
- Traffic light logic (if any)

#### `obstacle.rou.xml` - Routes and Vehicles
Vehicle types and traffic definitions:

**Vehicle Types:**
- `car`: Normal vehicle (accel: 2.0 m/s², decel: 4.5 m/s², length: 5m)
- `static`: Obstacle vehicle (minimal movement capabilities)

**Traffic:**
- `obstacle`: Blocks lane 0 at 100m position
- `car1` & `car2`: Moving vehicles (depart at 200s and 250s)

#### `obstacle.sumocfg` - Main Configuration
```xml
<configuration>
    <input>
        <net-file value="obstacle.net.xml"/>
        <route-files value="obstacle.rou.xml"/>
    </input>
    <processing>
        <time-to-teleport value="-1"/> <!-- Disables teleportation -->
    </processing>
</configuration>
```

## 🚀 Installation

### Prerequisites
- [SUMO](https://sumo.dlr.de/docs/Installing/index.html) traffic simulation package
- Python 3.7+ (optional, for analysis scripts)

### Quick Start
```bash
# Clone the repository
git clone https://github.com/yourusername/sumo-obstacle-simulation.git
cd sumo-obstacle-simulation

# Generate network (if needed)
netconvert -n obstacle.nod.xml -e obstacle.edg.xml -o obstacle.net.xml
```

## 💻 Usage

### Basic Simulation
```bash
# Run headless simulation
sumo -c obstacle.sumocfg

# Run with GUI for visualization
sumo-gui -c obstacle.sumocfg

# Run with step-by-step control
sumo-gui -c obstacle.sumocfg --start --quit-on-end
```

### Command Line Options
```bash
# Custom simulation duration
sumo -c obstacle.sumocfg --end 3000

# Enable XML output
sumo -c obstacle.sumocfg --tripinfo-output trips.xml

# Verbose output
sumo -c obstacle.sumocfg --verbose
```

## ⚙️ Configuration

### Vehicle Parameters

| Parameter | Car | Static | Description |
|-----------|-----|--------|-------------|
| `accel` | 2.0 | 0.1 | Acceleration (m/s²) |
| `decel` | 4.5 | 0.1 | Deceleration (m/s²) |
| `length` | 5.0 | 5.0 | Vehicle length (m) |
| `maxSpeed` | 13.9 | 0.1 | Maximum speed (m/s) |

### Lane-Changing Parameters
```xml
<vType id="car" lcStrategic="1.0" lcCooperative="1.0" 
       lcSpeedGain="1.0" lcKeepRight="0.0"/>
```

- `lcStrategic`: Strategic lane changes for route following
- `lcCooperative`: Cooperative behavior with other vehicles  
- `lcSpeedGain`: Lane changes for speed advantage
- `lcKeepRight`: Right-lane keeping preference

### Expected Behavior
1. 🚧 Static obstacle appears at 100m in lane 0
2. 🚗 Car1 enters at t=200s, Car2 at t=250s
3. 🔄 Vehicles detect obstacle and initiate lane change
4. 🤝 Cooperative gap acceptance between vehicles
5. ✅ Successful navigation around obstacle

## 🔧 Customization

### Traffic Flow Modifications
```xml
<!-- Add more vehicles -->
<vehicle id="car3" type="car" route="route0" depart="300"/>
<vehicle id="car4" type="car" route="route0" depart="350"/>

<!-- Change departure intervals -->
<flow id="traffic_flow" type="car" route="route0" begin="0" end="1000" 
      vehsPerHour="360"/>
```

### Obstacle Configuration
```xml
<!-- Move obstacle to different position/lane -->
<vehicle id="obstacle" type="static" route="route0" depart="0">
    <stop lane="edge0_1" startPos="200" duration="100000"/>
</vehicle>

<!-- Multiple obstacles -->
<vehicle id="obstacle2" type="static" route="route0" depart="0">
    <stop lane="edge0_0" startPos="300" duration="100000"/>
</vehicle>
```

### Network Extensions
```xml
<!-- Add more lanes -->
<edge id="edge0" from="start" to="end" priority="1" numLanes="3" speed="13.9"/>

<!-- Extend road length -->
<node id="end" x="1000" y="0" type="priority"/>
```

## 📊 Analysis

### Data Collection
```bash
# Generate trip information
sumo -c obstacle.sumocfg --tripinfo-output trips.xml

# Lane change information
sumo -c obstacle.sumocfg --lanechange-output lanechanges.xml

# Full trajectory data
sumo -c obstacle.sumocfg --fcd-output trajectories.xml
```

### Applications
- 🔬 **Research**: Lane-changing algorithm validation
- 📚 **Education**: Traffic dynamics demonstration  
- 🏗️ **Planning**: Traffic flow disruption analysis
- 🤖 **AI/ML**: Training data generation for autonomous vehicles
- 🚦 **Engineering**: Traffic management system testing

## 🐛 Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Vehicles teleport | Default teleportation enabled | Ensure `time-to-teleport="-1"` |
| No lane changes | Missing LC parameters | Check vehicle type definitions |
| Simulation crashes | File path/XML errors | Verify all file paths and XML syntax |
| Network generation fails | Node/edge inconsistency | Check coordinate and ID matching |

### Debug Commands
```bash
# Validate network
netconvert --node-files obstacle.nod.xml --edge-files obstacle.edg.xml --output-file test.net.xml

# Check route validity  
duarouter -n obstacle.net.xml -r obstacle.rou.xml --ignore-errors

# Verbose simulation output
sumo -c obstacle.sumocfg --verbose --log-file debug.log
```

### Performance Tips
- Use `--step-length 0.1` for higher precision
- Enable `--collision.check-junctions` for safety analysis
- Use `--duration-log.statistics` for performance metrics

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Guidelines
- Follow SUMO XML schema standards
- Include test cases for new features
- Update documentation for any changes
- Ensure backward compatibility

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📚 References

- [SUMO Documentation](https://sumo.dlr.de/docs/)
- [SUMO Lane-Changing Model](https://sumo.dlr.de/docs/Definition_of_Vehicles%2C_Vehicle_Types%2C_and_Routes.html#lane-changing_models)
- [Traffic Flow Theory](https://en.wikipedia.org/wiki/Traffic_flow)

## 🏷️ Tags

`sumo` `traffic-simulation` `lane-changing` `obstacle-avoidance` `transportation` `mobility`

---

**Made with ❤️ for the SUMO community**
