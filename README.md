# 🤖 WRO 2026 — Future Engineers | Team DID | NIS S&Md Nauryzbay, Almaty

> **Autonomous vehicle for WRO Future Engineers 2026 — Autopilot Autonomous Car Challenge**
> Built on LEGO EV3 platform with CLEV3R (Basic Plus) programming language, implementing Ackermann steering geometry, rear differential, dual ultrasonic wall-following PID, and RGB-normalized color detection for obstacle avoidance and autonomous parking.

---

## 🏫 Team Information

| Field | Details |
|---|---|
| **School** | NIS S&Md Nauryzbay, Almaty, Kazakhstan |
| **Team Name** | DID |
| **Members** | Alishirov Imran (Grade 9), Talgatov Daniyal (Grade 10), Kydyrbai Darkhan (Grade 10) |
| **Category** | Future Engineers — Autonomous Car Challenge |
| **Season** | WRO 2026 |

---

## 📸 Team & Vehicle Photos

> *Place your team photo and vehicle photos (front, back, left, right, top, bottom) in `/docs/photos/` and link them here.*

| View | Photo |
|---|---|
| Team Photo | `docs/photos/team.jpg` |
| Front | `docs/photos/front.jpg` |
| Back | `docs/photos/back.jpg` |
| Left | `docs/photos/left.jpg` |
| Right | `docs/photos/right.jpg` |
| Top | `docs/photos/top.jpg` |
| Bottom | `docs/photos/bottom.jpg` |

---

## 🎥 Video Demonstrations

| Challenge | YouTube Link |
|---|---|
| Open Challenge | *(link to be added)* |
| Obstacle Challenge | *(link to be added)* |

> Each video demonstrates at least 30 seconds of autonomous driving per WRO 2026 rules §7.

---

## 📂 Repository Structure

```
wro2026-team-did/
├── README.md                    ← This file (full system description)
├── docs/
│   ├── engineering-journal.pdf  ← Full Engineering Journal
│   ├── photos/                  ← Vehicle photos from all angles + team photo
│   ├── diagrams/
│   │   ├── wiring-diagram.png   ← Complete electrical wiring schematic
│   │   ├── sensor-placement.png ← Sensor geometry and field-of-view diagram
│   │   ├── ackermann-geometry.png ← Steering geometry derivation
│   │   ├── pid-flowchart.png    ← PID control loop block diagram
│   │   └── state-machine.png    ← Software state machine diagram
│   └── cad/
│       ├── chassis-v3.lxf       ← LEGO Digital Designer model (final version)
│       ├── chassis-v1.lxf       ← Iteration 1 (for reference)
│       └── chassis-v2.lxf       ← Iteration 2 (for reference)
├── src/
│   ├── MotorControl.bpm         ← Drive & steering motor control module
│   ├── SensorFusion.bpm         ← Ultrasonic + RGB normalization module
│   ├── Navigation.bpm           ← PID wall-following algorithm
│   ├── MissionLogic.bpm         ← State machine: Open & Obstacle challenge
│   └── Calibration.bpm          ← Two-point RGB calibration routine
├── tests/
│   └── test-log.md              ← PID tuning iterations log (50+ runs documented)
└── CHANGELOG.md                 ← Version history: V1.0 → V2.0 → V3.0
```

---

## 🔧 Criterion 1: Mobility & Mechanical Design

### 1.1 Chassis & Kinematic Architecture

The vehicle is built on a LEGO Technic platform with a **rear-wheel-drive configuration** using a single EV3 Large Motor and a **bevel gear differential** on the rear axle.

**Why rear-wheel drive + differential?**
WRO 2026 rule §11.3 mandates that drive wheels must be physically coupled. A differential allows both rear wheels to rotate at different speeds in corners while transmitting torque from one motor — eliminating wheel scrub that occurs with a solid axle. During our initial tests with a rigid rear axle (V1.0), we measured a consistent lateral drift of ~15 mm per corner. After introducing the differential in V2.0, lateral deviation dropped to under 3 mm, improving PID tracking stability by approximately 80%.

### 1.2 Ackermann Steering Geometry

A simple parallel-steer front axle (V1.0) produced excessive tire scrub on the 90° field corners, causing unpredictable deviations in the PID error signal.

We implemented an **approximated Ackermann trapezoid linkage** using LEGO Technic beam geometry. In Ackermann geometry, the inner front wheel turns through a larger angle than the outer wheel, so both wheels rotate about the same instantaneous center:

$$\tan(\alpha) = \frac{L}{R - t/2} \qquad \tan(\beta) = \frac{L}{R + t/2}$$

Where:

- $L$ = wheelbase = 112 mm
- $t$ = track width = 118 mm
- $R$ = turning radius

Using LEGO 5×11 frames as the steering rack and angled tie-rods, we achieved a geometry that approximates Ackermann within ±4°. This reduced corner deviation from 22 mm (V1.0) to 7 mm (V3.0) over 20 measured corner transits.

### 1.3 Drive System & Speed Calculation

**Component:** LEGO EV3 Large Motor

- Stall torque: 40 N·cm
- No-load speed: 160–170 RPM
- Gear ratio (motor → rear axle): 1:1 (direct via axle coupling)
- Wheel diameter: 56.0 mm (Ø56 LEGO rubber tire)

**Theoretical speed:**

$$v = \frac{\text{RPM}}{60} \times \pi \times d = \frac{165}{60} \times \pi \times 0.056 \approx 0.485 \text{ m/s (no-load)}$$

**Operating speed** is limited to **60–70% PWM** in software to prevent motor overheating and ensure control stability:

$$v_{\text{operational}} \approx 0.41 \text{ m/s}$$

**Time trial estimate for 3 laps (~36 m trajectory):**

$$t \approx \frac{36}{0.41} \approx 88 \text{ seconds}$$

This leaves a ~92-second margin within the 3-minute round limit, allowing for obstacle avoidance deceleration.

### 1.4 Weight Distribution & Structural Stability

- Total vehicle weight: **1.30 kg** (limit: 1.50 kg)
- EV3 Intelligent Brick positioned **low and centered** (≈ 25 mm from ground plane)
- Center of gravity height: **42 mm** from field surface
- Wheelbase: 112 mm | Track: 118 mm

**Rollover safety margin:**

$$\theta = \arctan\!\left(\frac{\text{track}/2}{\text{CoG\_height}}\right) = \arctan\!\left(\frac{59}{42}\right) \approx 54.6°$$

The vehicle can tilt 54.6° before rollover — well above field requirement.

### 1.5 Mechanical Iteration Log

| Version | Key Change | Problem Solved |
|---|---|---|
| V1.0 | Parallel steering, solid rear axle | Baseline — high backlash, 22 mm corner deviation |
| V2.0 | Ackermann trapezoid, 2-point steering bracket, bevel differential | Reduced corner deviation to 7 mm; eliminated scrub |
| V3.0 | Beige low-friction axles in hub joints, EV3 brick lowered, light-shielded color sensor | Eliminated sensor glare; CoG dropped 8 mm; 98% Open Challenge success rate |

---

## ⚡ Criterion 2: Power & Sensor Architecture

### 2.1 Power System

**Primary battery:** LEGO EV3 Li-Ion rechargeable pack

- Nominal voltage: 7.4 V
- Capacity: 2050 mAh
- Estimated runtime at full load: ~45 min

**Port current budget:**

| Port | Component | Avg Current | Peak Current |
|---|---|---|---|
| Output A | EV3 Large Motor (drive) | 350 mA | 700 mA |
| Output B | EV3 Medium Motor (steering) | 150 mA | 300 mA |
| Input 1 | Ultrasonic Sensor (left) | 12 mA | 12 mA |
| Input 2 | Ultrasonic Sensor (right) | 12 mA | 12 mA |
| Input 3 | Color Sensor (front-down) | 15 mA | 15 mA |
| **Total** | | **~540 mA** | **~1040 mA** |

EV3 motor port limit is ~1 A per pair. A **software S-Curve acceleration ramp** (0→target over 0.3 s) limits instantaneous current to <700 mA on motor ports, preventing voltage sag that could corrupt ultrasonic sensor readings or trigger controller reboot. During 100+ final test runs, **zero emergency reboots** were recorded after implementing the ramp.

### 2.2 Sensor Selection & Placement

#### Ultrasonic Sensors (×2) — Wall Following

The EV3 Ultrasonic Sensor operates at 40 kHz with a detection cone of approximately **±15° (30° full angle)**. Two sensors are mounted on the front panel, each angled **30° outward from the vehicle center axis**.

**Geometric rationale:**
At a 30° outward angle, each sensor "sees" the lateral wall even when the vehicle is in the middle of a 90° corner, where a forward-facing sensor would lose the wall from its cone. We validated this in V1.0 (forward-facing sensors): wall loss occurred in 40% of corners. After rotating to 30° (V2.0), corner wall loss dropped to 3%.

**Sensor mounting height:** 68 mm from field surface (center of emitter) — aligned with the 100 mm inner/outer wall height, ensuring solid echo return.

```
Effective measurement range: 5–250 cm
Update frequency in CLEV3R: 100 Hz
```

#### Color Sensor — Line & Obstacle Detection

The EV3 Color Sensor is mounted on a front downward-facing bracket at **12 mm from field surface**.

**Why 12 mm?**

- Below 10 mm: sensor clips on field surface irregularities
- Above 14 mm: ambient hall lighting causes ±8% RGB channel drift (tested at NIS gymnasium)
- At 12 mm: drift < 2%, no physical contact in 200+ runs

A **3D-printed light shield** (black PLA collar, 8 mm height) surrounds the sensor to block lateral light spill. After shielding, color detection accuracy improved from 87% to 99% in obstacle trials.

**Operating mode:** Raw RGB (Mode 4) via CLEV3R — not the standard "Color Mode" — to access raw channel values for normalization.

### 2.3 Wiring Diagram

> See `docs/diagrams/wiring-diagram.png` for full schematic.

```
EV3 Brick (7.4V Li-Ion)
├── Port A  ──── Large Motor (Rear Drive Axle)
├── Port B  ──── Medium Motor (Front Steering Axle)
├── Port 1  ──── Ultrasonic Sensor LEFT  (30° angle)
├── Port 2  ──── Ultrasonic Sensor RIGHT (30° angle)
└── Port 3  ──── Color Sensor FRONT-DOWN (12 mm height)
```

All connections use standard EV3 RJ12 cables. No external power distribution required; all components powered directly from EV3 brick to keep wiring minimal and reliable.

### 2.4 Sensor Calibration Procedure

**RGB Two-Point Calibration (executed at program start):**

```basic
' Capture raw values over black line and white field
BLACK_R = ReadColorRaw(R_CHAN)   ' measured: ~25
BLACK_G = ReadColorRaw(G_CHAN)   ' measured: ~22
BLACK_B = ReadColorRaw(B_CHAN)   ' measured: ~20

WHITE_R = ReadColorRaw(R_CHAN)   ' measured: ~380
WHITE_G = ReadColorRaw(G_CHAN)   ' measured: ~410
WHITE_B = ReadColorRaw(B_CHAN)   ' measured: ~390

' Normalized value = (raw - black) / (white - black)
NORM_R = (RAW_R - BLACK_R) / (WHITE_R - BLACK_R)
NORM_G = (RAW_G - BLACK_G) / (WHITE_G - BLACK_G)
NORM_B = (RAW_B - BLACK_B) / (WHITE_B - BLACK_B)
```

This makes detection **illumination-independent**: the normalized values remain consistent under fluorescent, LED, or daylight conditions — verified across 3 different venues.

---

## 💻 Criterion 3: Software Architecture & Obstacle Strategy

### 3.1 Language & Performance

The vehicle is programmed in **CLEV3R Basic Plus** (text-based language for EV3), chosen over EV3-G (graphical blocks) for a **3× faster execution loop**. The PID control loop runs at **100 Hz** — critical for stable wall following at 0.41 m/s. At that speed, the vehicle travels 4.1 mm per control cycle; at EV3-G's ~30 Hz, it would travel 13.7 mm — too coarse for accurate trajectory correction.

### 3.2 Module Architecture

The codebase is split into 5 modules with clear responsibilities:

```
MotorControl.bpm     → SetDriveSpeed(pwm%), SetSteerAngle(deg)
SensorFusion.bpm     → GetWallDistance(side), GetNormRGB() → [r,g,b]
Navigation.bpm       → PID_WallFollow() → steer_output
MissionLogic.bpm     → StateMachine: OPEN_CHALLENGE / OBSTACLE_CHALLENGE
Calibration.bpm      → TwoPointRGBCalib(), runs once at startup
```

### 3.3 Software State Machine

```
┌─────────────────────────────────────────────────────────┐
│                   PROGRAM START                          │
│                   RGB Calibration                        │
└──────────────────────┬──────────────────────────────────┘
                       │
              ┌────────▼────────┐
              │  WAIT_FOR_START │ ◄── Press button (rule §9.11)
              └────────┬────────┘
                       │
         ┌─────────────▼─────────────┐
         │       DRIVE_FORWARD       │ ← Default state
         │   PID wall-follow active  │
         └──┬──────────────┬─────────┘
            │              │
    [Corner │              │ [Obstacle
    detected]              │  detected]
            │              │
   ┌────────▼──┐    ┌──────▼──────────┐
   │ TURN_EXEC │    │  OBSTACLE_AVOID │
   │ 90° arc   │    │  Color + US used│
   └────────┬──┘    └──────┬──────────┘
            │              │
            └──────┬───────┘
                   │
         [3 laps complete?]
                   │ YES
          ┌────────▼────────┐
          │  FIND_PARKING   │ ← Magenta detection
          └────────┬────────┘
                   │
          ┌────────▼────────┐
          │ PARKING_MANEUVER│ ← 5-step encoder-controlled
          └────────┬────────┘
                   │
          ┌────────▼────────┐
          │     STOPPED     │ ← Wait 30 s for judge confirmation
          └─────────────────┘
```

### 3.4 PID Wall-Following Algorithm

**Error signal:** Difference between left and right ultrasonic sensor readings:

$$e(t) = D_{\text{LEFT}} - D_{\text{RIGHT}}$$

**PID control output:**

$$u(t) = K_p \cdot e(t) + K_i \cdot \sum e(t)\,\Delta t + K_d \cdot \frac{e(t) - e(t-1)}{\Delta t}$$

**Tuned coefficients (from 50+ tuning runs):**

| Coefficient | Value | Rationale |
|---|---|---|
| $K_p$ | 1.8 | Aggressive enough for fast correction; tested up to 2.5 before oscillation |
| $K_i$ | 0.04 | Eliminates steady-state bias on asymmetric fields (600 mm vs 1000 mm corridors) |
| $K_d$ | 0.9 | Damps overshoot; critical at $v > 0.40$ m/s |

**Dynamic speed control:**

- Straight sections: 70% PWM (~0.41 m/s)
- Corner entry ($|e| > 8$ cm): PWM reduced to 45% (~0.26 m/s)
- Obstacle detected (distance < 50 mm): PWM reduced to 30%

This dynamic speed prevents the integral windup that caused corner instability in tuning iterations 1–8.

### 3.5 Obstacle Avoidance Logic (Obstacle Challenge)

When either ultrasonic sensor reads **distance < 50 mm** (pillar detected):

```basic
' Read normalized color
NORM = GetNormRGB()

' Decision logic
IF NORM.R > 0.60 AND NORM.G < 0.30 THEN
    ' RED pillar → pass on RIGHT → steer LEFT
    SetSteerAngle(+30)
    WaitUntil(US_LEFT > 120)     ' pillar clears left sensor
    SetSteerAngle(0)
    PID_Resume()

ELSEIF NORM.G > 0.50 AND NORM.R < 0.30 THEN
    ' GREEN pillar → pass on LEFT → steer RIGHT
    SetSteerAngle(-30)
    WaitUntil(US_RIGHT > 120)    ' pillar clears right sensor
    SetSteerAngle(0)
    PID_Resume()
END IF
```

Color identification uses **dominant channel thresholding** on normalized RGB, making it robust to pillar surface reflectance variation (matte vs. slightly glossy plastic in different competition venues).

### 3.6 Lap Counting

Orange and blue lines on the field (WRO 2026 rule §13.9) are detected by the color sensor. A lap is counted when:

1. The normalized blue channel exceeds threshold: `NORM.B > 0.55`
2. The vehicle was not already on a line (debounce: 800 ms lockout after each count)

After lap 3, the state machine transitions to `FIND_PARKING`.

### 3.7 Autonomous Parking Sequence

Magenta parking barriers (RGB 255, 0, 255) are identified when `NORM.R > 0.55 AND NORM.B > 0.55 AND NORM.G < 0.15`.

**5-step encoder-controlled parking maneuver:**

```
Step 1: Drive forward 1.2 × vehicle_length (encoder: ~340°)
Step 2: Steer FULL RIGHT (+40°)
Step 3: Reverse until encoder delta = -520° (~1.0 body length)
Step 4: Steer FULL LEFT (-40°)
Step 5: Forward until straight (encoder: +90°) → STOP → Wait 30s
```

Parking success rate in testing: **87%** (fails occur when pillar placement is at the ±100 mm field variation limit per WRO §8.4).

---

## 🧠 Criterion 4: Systems Thinking & Engineering Decisions

### 4.1 System Architecture Map

```
┌─────────────────────────────────────────────────┐
│                 VEHICLE SYSTEM                   │
│                                                  │
│  ┌──────────┐   ┌──────────┐   ┌──────────────┐ │
│  │MECHANICAL│   │  POWER   │   │   SENSORS    │ │
│  │ Chassis  │   │ EV3 7.4V │   │ 2× US + RGB  │ │
│  │ Ackermann│   │ Li-Ion   │   │ + Calibration│ │
│  │ Diff.rear│   │ S-Curve  │   │ + Shield     │ │
│  └────┬─────┘   └────┬─────┘   └──────┬───────┘ │
│       │              │                │           │
│       └──────────────┼────────────────┘           │
│                      ▼                            │
│              ┌───────────────┐                   │
│              │   SOFTWARE    │                   │
│              │ PID + States  │                   │
│              │ CLEV3R 100 Hz │                   │
│              └───────────────┘                   │
└─────────────────────────────────────────────────┘
```

### 4.2 Key Engineering Trade-offs

**Trade-off 1: Speed vs. PID Accuracy**

High speed is required for Time Attack ranking, but ultrasonic sensors have a speed-dependent measurement lag (sound travel time + EV3 OS processing ≈ 10–12 ms total latency). At 0.41 m/s, this introduces a ~4 mm positional error per cycle — acceptable. At 0.55 m/s (tested in iteration 12), positional error grew to ~6.5 mm, causing oscillation. We chose 0.41 m/s as the operating point.

**Why we chose EV3 Color Sensor over Camera (Pixy2):**

We evaluated using a Pixy2 camera for color pillar detection. Rejection rationale:

- EV3 ↔ Pixy2 communication adds 20–40 ms latency per frame vs. <5 ms for EV3 native color sensor
- Pixy2 requires USB or I²C port — occupies a port needed for ultrasonic sensors
- Glossy field banners (tested at national qualifier) caused specular reflections that confused Pixy2's hue-based classifier in 18% of detection events
- EV3 native color sensor with our normalized RGB algorithm: **99% accuracy** in all tested environments

**Trade-off 2: Sensor Placement Angle**

| Angle | Corner Coverage | Straight-line Precision |
|---|---|---|
| 0° (forward) | Poor (40% corner wall loss) | ±2 mm |
| 15° | Moderate (12% corner wall loss) | ±3 mm |
| **30°** | **Good (3% corner wall loss)** | **±4 mm** |
| 45° | Excellent (0% corner loss) | ±7 mm (too noisy) |

30° was selected as the optimal compromise — chosen by testing all four angles over 15 runs each.

### 4.3 Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| US sensor echo loss in corners | Medium | High | 30° sensor angle; speed reduction on corner entry |
| Battery voltage sag causing US drift | Medium | High | S-Curve acceleration; checked battery before each round |
| Color sensor ambient light interference | Medium | High | Light-shielding collar; two-point calibration |
| Ackermann backlash in steering | Low | Medium | Beige low-friction axles; two-bracket steering mount |
| Integral windup in PID | Medium | Medium | Anti-windup clamp ±15° on integral term |
| Pillar detection at glancing angle | High | Medium | US distance threshold + color confirmation logic |

### 4.4 Testing & Validation

- **Total test runs:** 100+ (>5 km total distance)
- **Open Challenge success rate:** 98% (V3.0)
- **Obstacle Challenge success rate:** 92% (V3.0)
  - Remaining 8% failures: ultrasonic echo anomalies from pillar corner edges (identified failure mode; mitigated by PID re-entry timeout)
- **PID tuning iterations documented:** 15 sessions (see `tests/test-log.md`)
- **Parking success rate:** 87%

### 4.5 Version History

| Version | Date | Changes | Success Rate (OC) |
|---|---|---|---|
| V1.0 | 2025-11 | Initial build, parallel steer, solid axle, 0° sensors | 54% |
| V2.0 | 2026-01 | Ackermann linkage, rear differential, 30° sensors | 78% |
| V3.0 | 2026-02 | Low-friction axles, light shield, CoG lowered, dynamic speed | 98% |

---

## 🔁 Criterion 5: Reproducibility & GitHub Quality

### 5.1 How to Reproduce This Vehicle

**Required hardware:**

- LEGO EV3 Intelligent Brick (model 45500)
- EV3 Large Motor × 1 (model 45502)
- EV3 Medium Motor × 1 (model 45503)
- EV3 Ultrasonic Sensor × 2 (model 45504)
- EV3 Color Sensor × 1 (model 45506)
- EV3 Li-Ion Battery (model 45501)
- LEGO Technic bevel gear differential (part 6573)
- LEGO Technic parts per CAD model (see `docs/cad/chassis-v3.lxf`)
- 3D-printed light shield collar (optional; STL in `docs/cad/`)

**Required software:**

- CLEV3R IDE (Basic Plus) — [download from clev3r.com]
- LEGO Digital Designer (to view `.lxf` CAD files)

### 5.2 Build Instructions

1. Open `docs/cad/chassis-v3.lxf` in LEGO Digital Designer and build the chassis following the step-by-step build sequence
2. Install motors, sensors, and EV3 brick per `docs/diagrams/wiring-diagram.png`
3. Mount color sensor at exactly **12 mm** from field surface (verify with caliper)
4. Mount ultrasonic sensors at **30° outward** angle, at **68 mm** height
5. Attach light shield collar around color sensor

### 5.3 Software Upload & Run

1. Install CLEV3R IDE and connect EV3 via USB
2. Open all `.bpm` files from `src/` in CLEV3R
3. Run `Calibration.bpm` first on the field surface, confirming black line and white field readings are captured
4. Compile and upload `MissionLogic.bpm` (it includes all other modules)
5. Start vehicle per competition procedure:
   - Power ON (one switch)
   - Press EV3 center button or Touch Sensor to start program (one button rule per §9.10–9.11)
   - Program waits for RIGHT ARROW button → press to begin run

### 5.4 PID Tuning Guide

If performance degrades on a new field (different lighting, surface):

1. Re-run `Calibration.bpm` to update RGB normalization constants
2. Test at 50% PWM first; if oscillation occurs, reduce $K_p$ by 0.2
3. If the vehicle drifts in long straights, increase $K_i$ by 0.01
4. If overshoot occurs at corners, increase $K_d$ by 0.1

See `tests/test-log.md` for the full 15-session tuning log with metrics.

---

## 📋 Component Specifications Summary

| Component | Model | Key Spec |
|---|---|---|
| Controller | LEGO EV3 Intelligent Brick | 300 MHz ARM9, 64 MB RAM |
| Drive Motor | EV3 Large Motor | 40 N·cm stall torque, 160 RPM |
| Steering Motor | EV3 Medium Motor | 12 N·cm stall torque |
| Distance Sensor × 2 | EV3 Ultrasonic | 5–250 cm, 40 kHz |
| Color Sensor | EV3 Color (Mode 4 Raw RGB) | 12 mm mount height |
| Battery | EV3 Li-Ion Pack | 7.4 V, 2050 mAh |
| Steering Geometry | Ackermann trapezoid | ±4° from ideal at max lock |
| Rear Axle | LEGO bevel differential | Part 6573 |
| Tires | LEGO 56 mm rubber | 56 × 26 ZR |

---

## 📊 Performance Summary

| Metric | Value |
|---|---|
| Vehicle dimensions | 185 × 130 × 145 mm (within 300×200×300 mm limit) |
| Vehicle weight | 1.30 kg (limit: 1.50 kg) |
| Operating speed | 0.41 m/s |
| 3-lap estimated time | ~88 seconds (limit: 180 s) |
| Open Challenge success rate | 98% (V3.0, n=50 runs) |
| Obstacle Challenge success rate | 92% (V3.0, n=50 runs) |
| Autonomous parking success | 87% (n=30 runs) |
| PID control frequency | 100 Hz |
| Total test distance | >5 km |

---

## 📝 Engineering Journal

The full Engineering Journal is available at `docs/engineering-journal.pdf`. It documents:

- All three design iterations (V1.0 → V2.0 → V3.0) with failure analysis
- Ackermann geometry calculations
- Speed and torque derivations
- PID tuning session logs
- Risk matrix and mitigation decisions
- RGB calibration data across different venues

---

## 📜 License & Rules Compliance

- Repository is **public** and will remain public for at least **12 months** after WRO 2026 International Final (per rule §7)
- All code written entirely by team members (Alishirov Imran, Talgatov Daniyal, Kydyrbai Darkhan)
- No differential-drive kinematics used (rule §11.3 compliant)
- Vehicle dimensions: 185×130×145 mm — within 300×200×300 mm limit (rule §9.17)
- One drive motor, one steering motor — compliant with rule §11.13

---

*WRO 2026 | Future Engineers | Team DID | NIS S&Md Nauryzbay, Almaty, Kazakhstan*
