# HMI Wrapper Summary

## Overview
The HMI wrapper system provides a clean interface between Vue.js HMI and the V3ToolPLC backend. All wrappers extend `FB_HMIBase` and provide standardized status/command structures.

## Component Wrappers

### 1. **FB_HMISystemStatus** (System-Level Status)
**Purpose:** Read-only access to overall system state
**Location:** `POUs/System/FB_HMISystemStatus.TcPOU`

**Exposes:**
- Control mode (Manual/Auto/Maintenance)
- System readiness and interlock status
- PackML state and mode
- Active recipe information
- Operation sequencer status
- Overall system errors

**Methods:**
- `Init()` - Pass references to backend components
- `Cyclic()` - Update consolidated status
- `GetInterlockInfo()` - Get human-readable interlock details
- `AcknowledgeError()` - Clear system errors

---

### 2. **FB_MotionDevices** (WebDrive Motion)
**Purpose:** Control web handling system
**Location:** `POUs/Motion/FB_MotionDevices.TcPOU`

**Methods:**
- `Calibrate()` - Calibrate the web drive
- `Scroll(distance)` - Scroll web by distance
- `SetTension(value)` - Set web tension
- `Stop()` / `Halt()` - Stop motion
- `ResetAxes()` - Reset axis errors

---

### 3. **FB_HVPS** (High Voltage Power Supplies)
**Purpose:** Control 4 independent HV power supplies
**Location:** `POUs/HVPS/FB_HVPS.TcPOU`

**Methods:**
- `PowerOn(index)` / `PowerOff(index)` - Control input power
- `EnableHV(index)` / `DisableHV(index)` - Control HV output
- `SetVoltage(setpoint, index)` - Set voltage setpoint
- `SetCurrent(setpoint, index)` - Set current setpoint
- `GetSupplyStatus(index)` - Get individual supply status
- `FullShutdown()` - Emergency shutdown all supplies

---

### 4. **FB_BinaryDevice** (Discrete Devices)
**Purpose:** Generic wrapper for binary on/off devices
**Location:** `POUs/Discrete/FB_BinaryDevice.TcPOU`

**Used For:**
- AirKnives
- VacuumRelay
- VacuumPurge

**Commands:**
- `Enable` / `Disable` - Activate/deactivate device

---

### 5. **FB_Zaber** (Multi-Axis Motion)
**Purpose:** Control Zaber serial axis group (up to 8 axes)
**Location:** `POUs/Motion/Components/FB_Zaber.TcPOU`

**Methods:**
- `MoveAbsolute(axisIndex, position)` - Absolute positioning
- `MoveRelative(axisIndex, distance)` - Relative move
- `Home(axisIndex)` / `HomeAll()` - Homing operations
- `Stop(axisIndex)` / `StopAll()` - Stop axes
- `EnableAxis(index)` / `DisableAxis(index)` - Enable/disable
- `GetAxisStatus(index)` - Get individual axis status
- `P_AllAxesStatus` - Property for all axes status array
- `P_AxisCount` - Number of axes in group

---

### 6. **FB_HMIVision** (Vision System)
**Purpose:** Control vision inspection system
**Location:** `POUs/Vision/FB_HMIVision.TcPOU`

**Methods:**
- `Trigger()` - Trigger vision inspection

---

### 7. **FB_HMILaser** (Laser Control)
**Purpose:** Control Raylase laser system
**Location:** `POUs/Laser/FB_HMILaser.TcPOU`

**Methods:**
- `Fire()` - Start laser firing
- `StopFiring()` - Stop laser
- `SetPower(powerLevel)` - Set laser power (%)
- `P_LaserStatus` - Laser status including power level, firing state

---

## Main Wrapper: FB_HMIWrapper

**Location:** `POUs/FB_HMIWrapper.TcPOU`

### Structure:
```
FB_HMIWrapper
├── SystemStatus : FB_HMISystemStatus
├── Motion : FB_MotionDevices
├── HVPS : FB_HVPS
├── AirKnives : FB_BinaryDevice
├── VacuumRelay : FB_BinaryDevice
├── VacuumPurge : FB_BinaryDevice
├── Zaber : FB_Zaber
├── Vision : FB_HMIVision
└── Laser : FB_HMILaser
```

### Usage:
```
// In MAIN
HMIWrapper : FB_HMIWrapper;
V3Tool : FB_V3ToolPLC;

// Initialize (called once)
HMIWrapper.Init(
	// System-level references
	refControlMode := V3Tool.ControlMode,
	refSystemIntlk := V3Tool.SystemPermIntlk,
	refPackMLSequencer := V3Tool.PackMLSequencer,
	refRecipeManager := V3Tool.iRecipeManager,
	refOperationSequencer := V3Tool.iOpSequencer,

	// Component references
	refMotion := V3Tool.V3WebDrive.P_iWebDrive,
	refHVPS := V3Tool.HVPS,
	refAirKnives := V3Tool.iAirKnives,
	refVacuumRelay := V3Tool.iVacuumRelay,
	refVacuumPurge := V3Tool.iVacuumPurge,
	refZaber := V3Tool.iZaberAxes,
	refVision := V3Tool.iVision,
	refLaser := V3Tool.iRaylaseLaser
);

// Call every scan cycle
HMIWrapper.Cyclic();
```

---

## Data Structures (DUTs)

### Base Structures:
- `ST_HMIBase` - Base status, command, config
- `ST_HMIStatus` - Common status fields
- `ST_HMICommand` - Common command flags
- `ST_HMIConfiguration` - Common configuration

### Component-Specific:
- `ST_HMISystemStatus` - Overall system status
- `ST_HMIWebDriveStatus` - WebDrive motion status
- `ST_HMIHvpsStatus` - Single HVPS supply status
- `ST_HMIZaberAxisStatus` - Single Zaber axis status
- `ST_HMILaserStatus` - Laser status

---

## Not Wrapped (Backend Only)

These components are **not** exposed to HMI as they are backend orchestration:
- ControlMode (status read-only via SystemStatus)
- SystemPermIntlk (status read-only via SystemStatus)
- PackMLSequencer (status read-only via SystemStatus)
- RecipeManager (status read-only via SystemStatus)
- OperationSequencer (status read-only via SystemStatus)

---

## Next Steps

1. Test compilation of all wrappers
2. Verify Init() calls work with actual PLC components
3. Create Vue.js components to display/control each wrapper
4. Test Cyclic() updates in real-time
5. Implement error handling and validation
