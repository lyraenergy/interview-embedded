# Interview - Embedded Engineer

Goal: Demonstrate knowledge of embedded software and vehicle components in Python and C++.

## ECU Simulator

- Write a service in Python that is an ECU simulator.
- Answer Python and C++ questions regarding CAN below.

### Requirements

- Use standard tools like `socketcan` & `python-can`
- Use the defined `dbc` file below with messages and signals defined below
- Create a `vcan` (virtual CAN) interface
- Implement a Python function that loads the `dbc` file and parses the signal for speed in kph
- Write a function that increments a rolling 4-bit counter with each transmission
- Log all error messages and implement a service that keeps a log of errors and runtime events (to a file or systemd journal)
- Implement a Python function that decodes raw CAN bytes for `VehicleSpeedKph` and returns `kph`
- Follow Python conventions, include any dependencies, and a `README.md` with clear setup and run instructions
- Use Docker or virtual environment so anyone can easily run your code, including dependencies

`VCU.dbc`

```
VERSION "1.0"

BS_:

BU_: VCU

BO_ 256 VehicleSpeedKph: 8 VCU
 SG_ VehicleSpeedKph : 0|16@1+ (0.01,0) [0|2500] "km/h" VCU
 SG_ Counter         : 16|4@1+ (1,0)   [0|15]   ""      VCU
```

### Questions (Python)

- Decode `b'\x39\x30\x0a\x00\x00\x00\x00\x00'`. Provide the decoded dict with both `VehicleSpeedKph` and `Counter` values.
- How would you convert units in km/h into mph equivalent (and back)?
- If you send `VehicleSpeedKph=50.00, Counter=7` what CAN payload are you sending?
- What happens when the Counter reaches `0xF`? What comes next? How should a receiver use this counter to detect lost frames?
- Write a Python function that takes a payload and returns (VehicleSpeedKph, Counter) as a tuple

### Questions (C++)

- Write a small C++ function that takes an 8-byte array and returns a float speed in `km/h` and a `uint8_t` counter. Assume the encoding matches the DBC above.
- Implement a function in C++ that increments the 4-bit counter.
- Show how you would create a structure for `VehicleSpeedMsg` and write encode/decode functions that map this struct to/from an 8-byte CAN payload.
- Describe how you would abstract a CAN driver to work across both Linux socketcan and a microcontroller CAN

### Additional Questions (Extra Credit)

- In the Python service implement logic to detect when no VehicleSpeedKph message is received for >200 ms. Log an error and enter a fallback state.
- How would you detect `BUSOFF` or driver reset conditions from python-can and implement automatic recovery or reconnection.
- Simulate bad CAN frames (corruption or missing bytes). Show how your service detects and logs them.
- Write a monitor that checks the Counter increments sequentially and flags when it jumps or repeats unexpectedly.
- Extend your ECU simulator to publish a second message `VehicleSpeedMph`, derived from `VehicleSpeedKph`. What would you change?
- Provide a Dockerfile that sets up `python-can`, configures a `vcan` interface, runs your ECU simulator, and allows anyone to run tests with one command.