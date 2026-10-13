# Hybrid ACS v7.21 (flat)

A single-file, in-browser version of the Hybrid Spacecraft Attitude Control simulation. No build step, no dependencies: everything lives in `index.html`, and it runs on desktop, tablet, and phone.

## What it shows

- A spacecraft starting well away from its target attitude and slewing back under the hybrid controller, drawn against the target orientation
- Live readouts: time, control regime, pointing error, angular speed, energy ratio, damping gain, disturbance torque, and gyro bias
- Pointing error over time on a log scale, with the regime thresholds marked
- A quaternion memory bank of 1024 slots with a write pointer that wraps around when the buffer fills

## Controls

- **Pause / Reset** stop and restart the 25 s mission.
- **Entropy** switches randomness on or off and restarts the run. When on, each run begins from a random attitude and spin, the gyro bias drifts slowly, and a small random disturbance torque acts on the spacecraft throughout. When off, every run starts from the same 120° offset with no disturbances.
- **Speed** sets playback as a multiple of real time.

## Layout

The page adapts to screen size. The attitude view and error plot sit side by side when there is room and stack when there is not. The memory bank grid chooses its column count from the available width, using 32 columns on phones and 64 on wider screens.

## What is ported

A JavaScript port of `Hybrid.hs` from [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid):

- Quaternion math, exponential and logarithmic maps, double-cover handling
- Four-regime gain scheduling: acquisition, tracking, settling, fine-pointing
- Energy-ratio braking and 45 N·m torque saturation
- Star tracker and IMU noise
- Euler rotational dynamics with inertia 100 / 120 / 80
- A 25 s run at 100 Hz

## Differences from the Haskell

- The entropy features (random starts, gyro drift, disturbance torque) are additions. They are not in `Hybrid.hs`. Turning entropy off reproduces the original fixed scenario.
- The memory bank is a JavaScript model of the buffer in [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware). It does not run the Verilog or C code, and it has no busy flag.
- Position and velocity are omitted. Only attitude is simulated.
- The 3D view is a wireframe box, not a spacecraft model.
- Noise is not seeded, so runs differ.
- Output has not been checked against the compiled Haskell. The control logic is ported faithfully, but exact numbers may differ.

## Related repositories

| Repository | Role |
|---|---|
| [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid) | Current Haskell simulator |
| [SpaceCraft](https://github.com/subunits/SpaceCraft) | Earlier Haskell versions (v2.0 to v10.0) and two NASA ACS files |
| [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware) | Verilog memory-bank controller and C driver |
| [Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware](https://github.com/subunits/Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware) | UML diagrams covering the simulator and the hardware |

## Running

Open `index.html` in any modern browser.
