# Crazyflie Flight Deck

A ground station for the **Crazyflie 2.1 / 2.1+** in a single HTML file. Fly with a
**PS5 controller**, watch live attitude gauges, plot sensors, and edit parameters, all in
Chrome, with nothing to install.

![Fly tab](https://raw.githubusercontent.com/am1rlikespickles/crazyflie-firmware/claude/crazyflie-drone-gui-tstcii/gui/screenshot.png)

## Start in 30 seconds

1. Download `index.html` and open it in **Chrome** or **Edge** (double-click, or drag it into a tab).
2. Plug the PS5 controller in with a USB-C cable and press any button.
3. Switch the Crazyflie on, pick **Bluetooth**, press **Connect** and choose *Crazyflie-xxxxxx*.
4. Pick a flight mode (with a Flow deck, **Hover** is the easiest), press **△** to arm, then **✕** to take off.

No drone nearby? Pick **Simulator**: everything works the same, including the PS5 controller.

> If your Chromebook won't let a downloaded file use Bluetooth, host it instead:
> on GitHub open **Settings → Pages**, choose this branch and `/ (root)`, then open
> `https://<your-user>.github.io/crazyflie-firmware/gui/`.

## What it does

| | |
|---|---|
| **Connections** | Bluetooth LE (built into every Crazyflie 2.1/2.1+, no dongle) · Crazyradio PA / 2.0 over WebUSB · built-in simulator |
| **Controller** | PS5 DualSense (any standard gamepad works) · stick modes 1–4 · dead zone, expo, inverts · remappable buttons · rumble alerts · keyboard fallback |
| **Flight modes** | Manual (stabilized) · Altitude hold (barometer) · Height hold (Flow/Z-ranger) · Hover (Flow deck) · Position hold |
| **Instruments** | Artificial horizon with roll arc and pitch ladder · **half-circle roll and pitch gauges** with drone silhouettes, green/amber/red zones · compass · altitude tape with target · motor outputs · stick view · live packet bytes |
| **Safety** | Big emergency stop (Space / ○) · thrust lock · thrust slew · auto-land if the controller unplugs or the tab is hidden · low-battery rumble · supervisor status (tumbled, locked, can fly) |
| **Like cfclient** | Plotter with presets and CSV export · log TOC browser with custom log blocks and CSV recording · full parameter editor with persistent save · console · deck detection · LED-ring control · trim · flight presets |

## PS5 controls (Mode 2)

| Control | Manual | Assisted modes |
|---|---|---|
| Left stick ↕ | Throttle | Climb / descend |
| Left stick ↔ | Yaw | Yaw |
| Right stick | Tilt (roll / pitch) | Move |
| ✕ | – | Take off / land |
| △ | Arm / disarm | Arm / disarm |
| ○ | **Emergency stop** | **Emergency stop** |
| □ | Next flight mode | Next flight mode |
| L1 (hold) | Half speed | Half speed |
| D-pad / Create | Trim / reset trim | Trim / reset trim |

## Good to know

- **Browser**: Chrome or Edge on ChromeOS, macOS or Windows. Safari and Firefox lack Web Bluetooth and WebUSB. On Linux, enable `chrome://flags/#enable-experimental-web-platform-features`.
- **Bluetooth vs Crazyradio**: once a Crazyflie hears a Crazyradio it turns Bluetooth off until it restarts.
- **First connect** downloads the Crazyflie's variable tables, which takes about 10–20 s over Bluetooth.
  You can fly straight away while that happens; later connects use a cache and are instant.
- **Firmware**: needs the official Bitcraze firmware (tested against the 2026.08 protocol, with fallbacks
  for older releases). The mbed teaching firmware in this repo has no radio stack, so it can't be flown
  from any PC client. Flash the official `cf2-*.bin` with cfclient's bootloader to fly with this app.

## How it works

The page speaks **CRTP**, the Crazyflie's own protocol. Every packet format comes from the official sources:
`crazyflie-firmware` (log, param, commander, supervisor), `crazyflie-lib-python` (radio, safelink, setpoint signs),
`crazyflie-clients-python` (stick mapping, thrust slew) and `crazyflie2-nrf-firmware` (BLE service
`00000201-1c7f-4f9e-947b-43b7c00a9a08`, packet fragmentation). The Crazyflie answers each packet it receives
with at most one packet, so the app sends at a steady 100 packets/s from a Web Worker heartbeat that keeps
running in background tabs. Flight setpoints always jump the queue.
