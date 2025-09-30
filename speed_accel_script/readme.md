# 🚀 Klipper Speed Test Suite

<div align="center">

[![Demo Video](https://img.youtube.com/vi/LIaPKYyOujQ/0.jpg)](https://www.youtube.com/watch?v=LIaPKYyOujQ)

**Comprehensive performance testing macros for Klipper 3D printers**

Automatically find your printer's maximum safe speeds and accelerations with intelligent skipped-step detection.

![Klipper](https://img.shields.io/badge/Klipper-v0.11.0+-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active-success?style=flat-square)

[Features](#-features) • [Installation](#-installation) • [Quick Start](#-quick-start-guide) • [Documentation](#-macro-reference) • [Troubleshooting](#%EF%B8%8F-safety--troubleshooting)

</div>

---

## 🎯 Features

- ✅ **MAX_VELOCITY_TEST** - Find maximum speeds per axis
- ✅ **MAX_ACCEL_TEST** - Test acceleration limits with auto-detection
- ✅ **SCV_TEST** - Optimize Square Corner Velocity
- ✅ **BENCHMARK** - Reproducible performance testing with seeded random patterns
- ✅ **Automatic skipped-step detection** - Stops before damage occurs

> [!WARNING]
> **These tests measure mechanical limits - NOT print quality limits!**
> 
> Values found here are suitable for travel moves and understanding mechanical capabilities. For actual printing, always test with your specific materials and quality requirements.

---

## 📦 Installation

### Prerequisites

- Klipper firmware v0.11.0-276 or higher
- Working printer configuration
- Console access to Klipper

### Step 1: Download

Download `speed_test.cfg` and place it in your Klipper config directory (usually `~/printer_data/config/`).

### Step 2: Configure Printer Type

Edit `speed_test.cfg` and set your printer structure:

```gcode
[gcode_macro _USER_VARIABLES]
variable_structure: "cartesian"  # Options: "cartesian" or "corexy"
gcode:
```

### Step 3: Update printer.cfg

Add these lines to your `printer.cfg`:

```gcode
[include speed_test.cfg]

# Required modules for step detection
[respond]
[endstop_phase stepper_x]
[endstop_phase stepper_y]
```

### Step 4: Restart Klipper

```
FIRMWARE_RESTART
```

> [!TIP]
> Verify installation by typing `MAX_VELOCITY_TEST` in your console - you should see it autocomplete.

---

## 🚀 Quick Start Guide

<details>
<summary><b>📺 Watch Before Testing</b></summary>

- Ensure your printer is properly maintained (tight belts, lubricated rails)
- Clear the bed of any objects
- Be ready to hit Emergency Stop if needed
- Have a way to monitor console output

</details>

### Step 1: Test Maximum Velocity

Start conservative and increase gradually:

```gcode
# X-Axis - Full bed length movements
MAX_VELOCITY_TEST AXIS=X MIN_VELOCITY=50 MAX_VELOCITY=300 VELOCITY_INCREMENT=25 ACCEL=3000

# Y-Axis - Often slower on bed-slingers
MAX_VELOCITY_TEST AXIS=Y MIN_VELOCITY=50 MAX_VELOCITY=250 VELOCITY_INCREMENT=25 ACCEL=3000
```

**🎧 Listen for:**
- Grinding or rattling sounds
- Motor skipping
- Belt slipping

**✅ Validation test:**
```gcode
MAX_VELOCITY_TEST AXIS=X MAX_VELOCITY=250 DISTANCE=short REPEAT=100
```

### Step 2: Test Maximum Acceleration

```gcode
# X-Axis
MAX_ACCEL_TEST AXIS=X MIN_ACCEL=500 MAX_ACCEL=5000 ACCEL_INCREMENT=500 SPEED=150

# Y-Axis
MAX_ACCEL_TEST AXIS=Y MIN_ACCEL=500 MAX_ACCEL=4000 ACCEL_INCREMENT=500 SPEED=150
```

> [!NOTE]
> The test automatically stops if skipped steps are detected!

**✅ High-repetition validation:**
```gcode
MAX_ACCEL_TEST AXIS=X MAX_ACCEL=4000 REPEAT=200
```

### Step 3: Test Square Corner Velocity

```gcode
SCV_TEST MIN_SCV=1 MAX_SCV=15 SPEED=150 ACCEL=3000 CORNER_SIZE=50
```

### Step 4: Full System Benchmark

```gcode
BENCHMARK SPEED=200 ACCEL=3000 SCV=8 ITERATIONS=5 SEED=12345
```

> [!TIP]
> Use the same `SEED` value to get identical test patterns - perfect for A/B testing!

---

## 📖 Macro Reference

<details open>
<summary><h3>MAX_VELOCITY_TEST</h3></summary>

Tests maximum velocity on a single axis with configurable distance modes.

#### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `AXIS` | `X` | Axis to test: `X` or `Y` |
| `MIN_VELOCITY` | `10` | Starting velocity (mm/s) |
| `MAX_VELOCITY` | `300` | Maximum velocity (mm/s) |
| `VELOCITY_INCREMENT` | `10` | Step size between tests (mm/s) |
| `ACCEL` | max_accel | Acceleration value (mm/s²) |
| `DISTANCE` | `full` | `full` = max axis length, `short` = random shorter distances |
| `REPEAT` | `5` / `50` | Repetitions per velocity (5 for full, 50 for short) |

#### Examples

```gcode
# Conservative X-axis test
MAX_VELOCITY_TEST AXIS=X MAX_VELOCITY=200

# Aggressive test with fine steps
MAX_VELOCITY_TEST AXIS=X MIN_VELOCITY=200 MAX_VELOCITY=500 VELOCITY_INCREMENT=10 ACCEL=5000

# Quick validation with short movements
MAX_VELOCITY_TEST AXIS=Y MAX_VELOCITY=300 DISTANCE=short REPEAT=200
```

#### Console Output Example

```
===== MAX_VELOCITY_TEST START =====
Axis: X | Accel: 3000 mm/s² | Range: 50-300 mm/s
Steps: 25 mm/s | Repeats: 5 | Distance mode: full
Test area: 20.0 to 330.0 mm (Range: 310.0 mm)
===================================
[0%] Testing 50 mm/s @ 3000 mm/s²
[20%] Testing 100 mm/s @ 3000 mm/s²
[40%] Testing 150 mm/s @ 3000 mm/s²
...
```

</details>

<details>
<summary><h3>MAX_ACCEL_TEST</h3></summary>

Tests maximum acceleration with automatic skipped-step detection.

#### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `AXIS` | `X` | Axis to test: `X` or `Y` |
| `MIN_ACCEL` | `100` | Starting acceleration (mm/s²) |
| `MAX_ACCEL` | `1000` | Maximum acceleration (mm/s²) |
| `ACCEL_INCREMENT` | `100` | Step size between tests (mm/s²) |
| `SPEED` | max_velocity | Velocity for test (mm/s) |
| `REPEAT` | `50` | Repetitions per acceleration value |
| `MIN_DISTANCE` | `50` | Minimum movement distance (mm) |

#### Examples

```gcode
# Standard test
MAX_ACCEL_TEST AXIS=X MIN_ACCEL=1000 MAX_ACCEL=8000 ACCEL_INCREMENT=500

# High-speed acceleration test
MAX_ACCEL_TEST AXIS=X MIN_ACCEL=2000 MAX_ACCEL=10000 SPEED=200 REPEAT=100

# Fine-tuning near the limit
MAX_ACCEL_TEST AXIS=Y MIN_ACCEL=4000 MAX_ACCEL=5000 ACCEL_INCREMENT=100
```

#### Console Output Example

```
===== MAX_ACCEL_TEST START =====
Axis: X | Speed: 150 mm/s | Range: 500-5000 mm/s²
Steps: 500 mm/s² | Repeats: 50 | Min distance: 50 mm
Test area: 20.0 to 330.0 mm (Range: 310.0 mm)
================================
[0%] Testing 150 mm/s @ 500 mm/s²
[11%] Testing 150 mm/s @ 1000 mm/s²
[22%] Testing 150 mm/s @ 1500 mm/s²
...
```

</details>

<details>
<summary><h3>SCV_TEST</h3></summary>

Tests Square Corner Velocity using three different corner patterns.

#### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `MIN_SCV` | `1` | Starting SCV value (mm/s) |
| `MAX_SCV` | `20` | Maximum SCV value (mm/s) |
| `SCV_INCREMENT` | `1` | Step size between tests (mm/s) |
| `SPEED` | `100` | Test velocity (mm/s) |
| `ACCEL` | `3000` | Acceleration value (mm/s²) |
| `CORNER_SIZE` | `50` | Pattern dimensions (mm) |
| `REPEAT` | `3` | Pattern repetitions per SCV value |

#### Test Patterns

1. **Square Pattern** - Classic 90° corners for baseline performance
2. **Figure-8 Pattern** - Diagonal movements with direction changes
3. **Zigzag Pattern** - Extreme rapid direction reversals

#### Examples

```gcode
# Standard SCV test
SCV_TEST MIN_SCV=1 MAX_SCV=15 SPEED=150 ACCEL=3000

# Fine-tuning SCV
SCV_TEST MIN_SCV=8 MAX_SCV=12 SCV_INCREMENT=0.5 SPEED=200

# Quick test with smaller pattern
SCV_TEST MAX_SCV=10 CORNER_SIZE=30 REPEAT=2
```

#### Console Output Example

```
======== SCV_TEST START ========
Speed: 150 mm/s | Accel: 3000 mm/s²
SCV Range: 1-15 mm/s | Steps: 1 mm/s
Pattern size: 50x50 mm | Repeats: 3
Test patterns: Square, Figure-8, Zigzag
===================================
[0%] Testing SCV: 1 mm/s (3 patterns)
[7%] Testing SCV: 2 mm/s (3 patterns)
[14%] Testing SCV: 3 mm/s (3 patterns)
...
```

</details>

<details>
<summary><h3>BENCHMARK</h3></summary>

Comprehensive performance test with reproducible random movement patterns.

#### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `SPEED` | max_velocity | Test velocity (mm/s) |
| `ACCEL` | max_accel | Acceleration value (mm/s²) |
| `SCV` | printer setting | Square corner velocity (mm/s) |
| `ITERATIONS` | `1` | Number of complete test cycles |
| `BOUND` | `40` | Safety margin from bed edges (mm) |
| `SMALLPATTERNSIZE` | `20` | Dimensions of small pattern (mm) |
| `ZPOS` | `20` | Z-height during test (mm) |
| `CRUISE_RATIO` | `0.0` | Minimum cruise ratio (0 = disabled) |
| `SEED` | `12345` | Random seed for reproducible patterns |

#### Test Patterns

Each iteration includes:
- Large diagonal movements
- Full bed perimeter box
- Small centered diagonal movements  
- Small centered box pattern
- Pseudo-random fill movements (seeded)

#### Examples

```gcode
# Standard benchmark
BENCHMARK SPEED=250 ACCEL=4000 SCV=8 ITERATIONS=5

# Stress test with many iterations
BENCHMARK SPEED=300 ACCEL=6000 SCV=10 ITERATIONS=20 SEED=42

# A/B comparison test (use same seed!)
BENCHMARK SPEED=200 ACCEL=3000 ITERATIONS=10 SEED=99999
```

#### Console Output Example

```
======== BENCHMARK START ========
Speed: 250 mm/s | Accel: 4000 mm/s² | SCV: 8 mm/s
Iterations: 5 | Seed: 12345 | Z-height: 20 mm
Test area: X40-310 Y40-310 mm
Small pattern: 20x20 mm at center
==================================
Running iteration 1/5...
Running iteration 2/5...
...
```

> [!TIP]
> **Reproducible Testing:** Using the same `SEED` generates identical movement patterns. Perfect for:
> - Comparing before/after modifications
> - Testing different belt tensions
> - Evaluating Input Shaper settings
> - Benchmarking firmware updates

</details>

---

## 🎓 Recommended Testing Workflow

### Phase 1: Find Maximum Velocity 🏃

**Goal:** Determine the fastest safe speed for each axis.

```gcode
# Start conservative
MAX_VELOCITY_TEST AXIS=X MIN_VELOCITY=50 MAX_VELOCITY=200 DISTANCE=full

# Push boundaries if successful
MAX_VELOCITY_TEST AXIS=X MIN_VELOCITY=200 MAX_VELOCITY=400 VELOCITY_INCREMENT=50

# Validate with short movements
MAX_VELOCITY_TEST AXIS=X MAX_VELOCITY=350 DISTANCE=short REPEAT=100
```

**🎧 Stop immediately if you hear:**
- Grinding noises
- Belt slipping sounds
- Motor skipping
- Excessive vibration

> [!NOTE]
> **Bed-slinger printers:** Y-axis typically achieves 20-40% lower speeds than X-axis due to bed mass.

### Phase 2: Find Maximum Acceleration 🚀

**Goal:** Test acceleration limits with automatic safety cutoff.

```gcode
# Initial test
MAX_ACCEL_TEST AXIS=X MIN_ACCEL=1000 MAX_ACCEL=5000 SPEED=150

# If successful, push higher
MAX_ACCEL_TEST AXIS=X MIN_ACCEL=5000 MAX_ACCEL=10000 ACCEL_INCREMENT=500

# High-repetition validation
MAX_ACCEL_TEST AXIS=X MAX_ACCEL=6000 REPEAT=200
```

> [!IMPORTANT]
> The test will automatically stop if skipped steps are detected, but you should still monitor for mechanical problems!

### Phase 3: Optimize Square Corner Velocity 📐

**Goal:** Find the best SCV for sharp corners without stuttering.

```gcode
SCV_TEST MIN_SCV=1 MAX_SCV=15 SPEED=150 ACCEL=3000
```

Watch for resonance or stuttering during rapid direction changes.

### Phase 4: Apply Safety Margins 🛡️

Never run at absolute maximum values! Apply these reductions:

| Parameter | Found Value | Safety Margin | Final Value |
|-----------|-------------|---------------|-------------|
| **Velocity** | 400 mm/s | -20% | **320 mm/s** |
| **Acceleration** | 8000 mm/s² | -30% | **5600 mm/s²** |
| **SCV** | 12 mm/s | -10% | **11 mm/s** |

**Why safety margins?**
- ✅ Protects mechanical components from wear
- ✅ Prevents layer shifting under real printing conditions
- ✅ Accounts for filament weight and nozzle drag
- ✅ Provides headroom for environmental factors

### Phase 5: Final Validation ✅

Run comprehensive benchmarks with your final values:

```gcode
BENCHMARK SPEED=320 ACCEL=5600 SCV=11 ITERATIONS=10 SEED=12345
```

This should complete without any errors or step loss.

> [!TIP]
> **Document everything!** Keep a log of your test results, settings, and any observations. This is invaluable when troubleshooting later.

---

## ⚠️ Safety & Troubleshooting

### 🚨 Emergency Stop Protocol

> [!CAUTION]
> **These macros CANNOT be gracefully stopped mid-execution!**

If you encounter problems during testing:

1. **Immediately press Emergency Stop** (or power off)
2. Inspect printer for mechanical issues
3. Check belt tension, pulley set screws, motor mounts
4. Lower test parameters by 30-40%
5. Restart with more conservative values


### Printer Configuration Differences

#### Cartesian Mode
- Tests single axis independently
- Only homes the axis being tested
- Faster testing process
- Less motor wear

#### CoreXY Mode  
- Always homes both X and Y (mechanically coupled)
- Both motors active during all tests
- More thorough system testing
- Accounts for motor interaction

Configure in `_USER_VARIABLES` macro.

## 📚 Additional Resources

- [Ellis' Print Tuning Guide](https://ellis3dp.com/Print-Tuning-Guide/) - Original inspiration
- [Klipper Documentation](https://www.klipper3d.org/) - Official Klipper docs
- [Klipper Discourse](https://klipper.discourse.group/) - Community support

---

## 📄 License

MIT License - Feel free to use, modify, and share!

---

## 🎬 Credits

- **Original Concept:** [Ellis' Print Tuning Guide](https://ellis3dp.com/Print-Tuning-Guide/articles/determining_max_speeds_accels.html)
- **Implementation:** Fragmon [crydteam]
- **Updated:** 2025
- **Minimum Klipper Version:** v0.11.0-276

---

<div align="center">

**⚠️ Remember: These are diagnostic tools!**

Always apply appropriate safety margins to values you find.

Your print quality requirements may demand lower values than mechanical limits.

**Happy testing! 🚀**

</div>
