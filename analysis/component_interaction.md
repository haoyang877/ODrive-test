# ODrive Component Interaction Analysis

## Component Interaction Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           External Interfaces                              │
│                                                                             │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐     │
│  │    GUI      │   │ Python Tool │   │  Arduino    │   │ Third Party │     │
│  │  (Vue.js)   │   │ (odrivetool)│   │   Library   │   │    Apps     │     │
│  └──────┬──────┘   └──────┬──────┘   └──────┬──────┘   └──────┬──────┘     │
│         │                 │                 │                 │            │
└─────────┼─────────────────┼─────────────────┼─────────────────┼────────────┘
          │                 │                 │                 │
          ▼                 ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     Communication Protocols                                │
│                                                                             │
│  ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────────────┐   │
│  │ Fibre Protocol  │   │  ASCII Protocol │   │      CAN Protocol       │   │
│  │                 │   │                 │   │                         │   │
│  │ • Type Safe     │   │ • Human Read    │   │ • Real-time            │   │
│  │ • Auto Discovery│   │ • Simple Debug  │   │ • Network Capable      │   │
│  │ • Cross Platform│   │ • Low Bandwidth │   │ • Low Latency          │   │
│  └─────────────────┘   └─────────────────┘   └─────────────────────────┘   │
│            │                      │                         │              │
└────────────┼──────────────────────┼─────────────────────────┼──────────────┘
             │                      │                         │
             ▼                      ▼                         ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                      Communication Interface Layer                         │
│                                                                             │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐     │
│  │     USB     │   │    UART     │   │     I2C     │   │     CAN     │     │
│  │             │   │             │   │             │   │             │     │
│  │ interface_  │   │ interface_  │   │ interface_  │   │ interface_  │     │
│  │ usb.cpp     │   │ uart.cpp    │   │ i2c.cpp     │   │ can.hpp     │     │
│  └─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘     │
│           │               │               │               │                │
└───────────┼───────────────┼───────────────┼───────────────┼────────────────┘
            │               │               │               │
            ▼               ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ODrive Core System                                 │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │                        Configuration System                           │ │
│  │                                                                       │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │ │
│  │  │   Config    │  │     NVM     │  │  Interface  │  │   Error     │  │ │
│  │  │  Manager    │  │  Storage    │  │  Generator  │  │  Handling   │  │ │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘  │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                      │                                     │
│  ┌───────────────────────────────────┼───────────────────────────────────┐ │
│  │                    ODrive Object  │  (Main System Container)         │ │
│  │                                   ▼                                   │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │ │
│  │  │   Axis 0    │  │   Axis 1    │  │    CAN      │  │   System    │  │ │
│  │  │             │  │             │  │   Node      │  │   Monitor   │  │ │
│  │  │  ┌───────┐  │  │  ┌───────┐  │  │             │  │             │  │ │
│  │  │  │ Motor │  │  │  │ Motor │  │  └─────────────┘  └─────────────┘  │ │
│  │  │  └───────┘  │  │  └───────┘  │                                    │ │
│  │  │  ┌───────┐  │  │  ┌───────┐  │                                    │ │
│  │  │  │Encoder│  │  │  │Encoder│  │                                    │ │
│  │  │  └───────┘  │  │  └───────┘  │                                    │ │
│  │  │  ┌───────┐  │  │  ┌───────┐  │                                    │ │
│  │  │  │ Ctrl  │  │  │  │ Ctrl  │  │                                    │ │
│  │  │  └───────┘  │  │  └───────┘  │                                    │ │
│  │  └─────────────┘  └─────────────┘                                    │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           FreeRTOS Task System                             │
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐   │
│  │   Axis      │  │    USB      │  │    CAN      │  │  Communication  │   │
│  │   Task      │  │   Task      │  │   Task      │  │     Tasks       │   │
│  │             │  │             │  │             │  │                 │   │
│  │ • Control   │  │ • Fibre     │  │ • CAN Rx/Tx │  │ • UART Handler  │   │
│  │   Loop      │  │   Protocol  │  │ • Node Mgmt │  │ • I2C Handler   │   │
│  │ • Safety    │  │ • Device    │  │ • Real-time │  │ • Protocol Proc │   │
│  │   Monitor   │  │   Config    │  │   Control   │  │                 │   │
│  │ • 8-40 kHz  │  │ • Streaming │  │             │  │                 │   │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────┘   │
│         │                │                │                 │             │
└─────────┼────────────────┼────────────────┼─────────────────┼─────────────┘
          │                │                │                 │
          ▼                ▼                ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Hardware Abstraction Layer                          │
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐   │
│  │    PWM      │  │     ADC     │  │    GPIO     │  │     Timers      │   │
│  │             │  │             │  │             │  │                 │   │
│  │ • 3-Phase   │  │ • Current   │  │ • Encoder   │  │ • Control Timer │   │
│  │ • Deadtime  │  │ • Voltage   │  │ • Status    │  │ • PWM Timer     │   │
│  │ • Frequency │  │ • Temp      │  │ • Endstops  │  │ • ADC Timer     │   │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Data Flow Patterns

### 1. Real-Time Control Loop (8-40 kHz)
```
Encoder → Position Estimation → Controller → Current Control → PWM → Motor
   ↑                                                               ↓
   ←───────── Current Feedback ←─ ADC Sampling ←─ Current Sensors ←
```

### 2. Configuration Flow
```
User Interface → Communication Protocol → Configuration System → NVM Storage
                                        ↓
                               Runtime Parameter Update
                                        ↓
                                Motor Control System
```

### 3. Monitoring and Telemetry
```
Motor Control System → Data Collection → Communication Buffer → Protocol Stack
                                                                      ↓
                                                              User Interface
```

## Component Dependencies

### High-Level Dependencies
- **GUI** → Fibre Protocol → USB Interface → ODrive Core
- **Python Tools** → Fibre Protocol → USB/CAN Interface → ODrive Core
- **Arduino Library** → CAN Protocol → CAN Interface → ODrive Core

### Core System Dependencies
- **ODrive Core** → Configuration System → NVM Storage
- **Axis Components** → Motor Control → Hardware Abstraction
- **Communication** → Protocol Stack → Interface Layer

### Low-Level Dependencies
- **All Components** → FreeRTOS → STM32 HAL → Hardware

## Inter-Component Communication

### Synchronous Communication
- **Configuration Access**: Direct function calls with mutex protection
- **Real-time Control**: Direct memory access for performance-critical paths
- **Hardware Interface**: Register access through HAL

### Asynchronous Communication
- **Task Communication**: FreeRTOS queues and semaphores
- **Event Handling**: Interrupt-driven event propagation
- **Protocol Processing**: Buffered message handling

### Data Sharing
- **Global State**: Protected by mutexes and atomic operations
- **Configuration Data**: Centralized configuration manager
- **Telemetry Data**: Circular buffers for streaming data

This component interaction model ensures:
1. **Separation of Concerns**: Each component has clear responsibilities
2. **Loose Coupling**: Components interact through well-defined interfaces
3. **High Cohesion**: Related functionality is grouped together
4. **Scalability**: New components can be added without major restructuring