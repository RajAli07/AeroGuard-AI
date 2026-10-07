# AeroGuard AI Simulator

Browser-based simulation of **AeroGuard AI**, an edge-intelligent preventive safety system for motorcycle riders. It models a virtual rider, helmet sensors, an on-device risk engine, multi-modal warnings and a verified post-crash SOS workflow. No install, no dependencies: open `index.html`.

**Live demo:** `https://<your-username>.github.io/aeroguard-sim/` (after enabling GitHub Pages, see below)

## What is simulated

| Layer | Details |
|---|---|
| Rider and bike | Speed controller, throttle and brake events, weaving (yaw rate) and lean angle from `atan(v * yaw_rate / g)` |
| Sensors | 50 Hz IMU (ax, ay, gyro z, roll) with adjustable Gaussian noise; 1 Hz GPS with noise and on/off switch |
| Risk engine | First 30 s calibrate a personal baseline. Each second, 5 features over a 5 s window (accel std, peak braking, yaw std, roll std, mean lateral accel) are scored by deviation from the baseline, combined with speed and lean-at-speed terms into a 0 to 100 Dynamic Risk Score (DRS) |
| Stabilisation | Asymmetric smoothing (fast up, slow down), hysteresis on state thresholds (30 / 60 / 85), 10 s alert cooldown, samples above 4 g kept out of the risk engine |
| Warnings | Level 1 haptic pulse, level 2 voice "Reduce speed", level 3 alarm + voice; AeroDash LEDs and display mirror the state |
| Closed loop | Optional "rider obeys warnings" model: risky behaviour is damped after a delay, so the score falls |
| Crash response | Impact above 4 g, then roll above 60 degrees, 2 s stillness and GPS speed near zero (or GPS unavailable). 15 s countdown with cancel. SOS message with coordinates; queued and retried if the phone has no network |
| False alarms | "Helmet drop" scenario produces a 12 g spike with no orientation change: dismissed, no SOS |
| Degraded modes | GPS lost for 5 s disables the speed term; high sensor noise; no network |

## Scenarios

Calm commute, Aggressive riding, Fatigue drift (steering instability grows over time), Helmet drop (false alarm), Crash.

## Run locally

Open `index.html` in any modern browser. Click a scenario once to enable sound.

## Publish on GitHub Pages

```bash
git init
git add .
git commit -m "AeroGuard AI simulator"
git branch -M main
git remote add origin https://github.com/<your-username>/aeroguard-sim.git
git push -u origin main
```
Then in the repository: Settings, Pages, Source "Deploy from a branch", branch `main`, folder `/ (root)`.

## Honest limitations

- The baseline model is a statistical deviation scorer, not a trained neural network. It demonstrates the pipeline and thresholds; it does not prove real-world accuracy.
- The rider, road and crash dynamics are simplified synthetic models. Thresholds (4 g, 60 degrees, 2 s) must be validated on real ride and crash-test data.
- This is risk detection, not accident prediction. It is a student prototype and not a certified safety device.

## Roadmap

Python port with scikit-learn Isolation Forest and a 1D-CNN autoencoder, precision/recall evaluation on labelled rides, TFLite Micro export for ESP32, hardware-in-the-loop with MPU6050 and NEO-6M.

## License

MIT
