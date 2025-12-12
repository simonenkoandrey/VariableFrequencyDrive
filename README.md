# Variable Frequency Drive (VFD) 

Variable Frequency Drive (VFD) — a repository containing firmware, documentation and reference materials for controlling three-phase AC motors by varying output frequency and voltage. This project provides a safe, configurable platform for learning, experimentation and small to medium custom VFD applications.

Key goals
- Provide a clear reference implementation of motor control concepts (open-loop V/Hz, closed-loop control, ramps).
- Keep hardware and software modular so different control boards and power stages can be used.
- Emphasize safety, testing, and reproducible builds.

Status
- Repository contents may include firmware, configuration examples, schematic references and test procedures. Check the repo tree for available boards, drivers and example code.

Features
- Frequency control with adjustable ramp-up / ramp-down (slew rate)
- Open-loop V/Hz control profile (configurable V/F curve)
- Support for motor direction, start/stop, braking
- Support for external speed reference: potentiometer, analog input, UART/Modbus, or PWM
- Current sensing and overcurrent protection hooks
- Basic telemetry and debugging output (serial / CAN / Modbus — depending on build)
- Safety interlocks (emergency stop, undervoltage, overtemperature) integrated in software

Architecture overview
- Control board: MCU-based controller (STM32, ESP32, AVR, etc.) running real-time firmware to produce PWM signals and read sensors.
- Power stage: inverter (IGBT or MOSFET bridge) with gate drivers, DC bus (rectifier + bulk capacitors).
- Sensing: current shunts or hall sensors for phase or DC current; voltage sensing for DC bus and optionally motor phases; temperature sensors on heat sinks.
- Human / network interface: local buttons, rotary encoder, display, and/or remote interface via UART / Modbus / CAN / web UI.

Hardware (what you should expect)
- Motor: 3-phase induction motor (specs documented per-test)
- Rectifier and DC link capacitor sized for motor power and expected ripple
- Inverter stage: matched to motor current and switching frequency
- Proper gate drivers and isolation (recommended) for safety
- Cooling: heatsink + fan rated for dissipation
- Safeguards: fuses, contactor, emergency stop

Quick start — cloning and exploring
1. Clone the repository:
   git clone https://github.com/simonenkoandrey/VariableFrequencyDrive.git
   cd VariableFrequencyDrive

2. Inspect available targets and docs:
   - Look for folders like `firmware/`, `hardware/`, `docs/`, `examples/`.

3. Build and flash (examples — adapt to the project's toolchain):
   - PlatformIO:
     pio run -e <env>            # build
     pio run -e <env> -t upload  # flash
   - Make (example):
     make BOARD=<board> all
     make BOARD=<board> flash

Configuration
- Main configuration typically lives in a file like `config.h`, `platformio.ini`, or `settings.json`.
- Important parameters:
  - Motor rated frequency and voltage
  - PWM switching frequency and dead-time
  - Current limits and ADC scaling
  - Acceleration / deceleration ramps
  - Sensor calibration (shunt gain, offset)
- Always calibrate sensors on the bench using known references before connecting the motor.

Typical operation flow
1. Power up with no motor connected and verify DC bus voltage.
2. With motor disconnected, enable PWM at low frequency and verify correct waveform on a scope.
3. Connect motor, run at very low frequency and observe current/temperature.
4. Gradually raise frequency while monitoring currents and temperatures.

Safety & testing (read before powering)
- Never power or test without appropriate fusing, isolation, and protective gear.
- Use an isolation transformer when possible during early testing.
- Verify dead-times and shoot-through protection on the power stage.
- Implement and test emergency-stop behavior and contactor interlocks.
- Use a current-limited bench supply for low-power smoke tests before connecting the full rectifier.

Troubleshooting tips
- No output PWM: check clock config, timers and PWM pin mapping.
- High idle current: verify PWM gating and inverter dead-time, check for short circuits.
- Overcurrent trips: check sensor wiring, ADC calibration, and current-sense orientation.
- Motor hum but no torque: incorrect phase sequence or insufficient voltage at target frequency (V/F profile).

Contributing
- Please open issues for bugs or enhancement requests.
- Follow the repository's coding style and commit message conventions (see CONTRIBUTING.md if present).
- Include hardware revision and MCU/board information when reporting issues or submitting PRs.

Documentation and additional resources
- Check the `docs/` folder for hardware schematics, BOMs, and calibration procedures.
- Example files and test scripts are usually under `examples/` or `tools/`.

Licensing
- This repository is distributed under the MIT License — see LICENSE for details.
- Replace or change license in accordance with the project needs.

Acknowledgements
- Based on common motor-control techniques and community best practices. Please credit code/libs used from third parties as required by their licenses.

Contact / Maintainers
- Repository owner: simonenkoandrey
- For feature requests or security issues, open an issue or PR.

Notes
- This README is a general guide. Check the repository contents for precise build instructions and hardware-specific documentation.
- If you want, I can tailor this README for a specific MCU (STM32, ESP32, AVR) or add detailed build commands and an example V/F table — tell me which target board and toolchain you use.
