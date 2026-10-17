# Morph

**A 17-DOF articulating robotic chain that folds itself into any shape.**

Morph is a reconfigurable robot inspired by the microbots from *Big Hero 6*. Text it a request like "make a chair", and it plans, simulates, and physically folds its 17-module body into that shape.

Built at **Hack the North 2026**, where it placed **top 30 out of 349 projects** and was selected for the project showcase.

[Devpost](https://devpost.com/software/cubes-i3g1ue)

---

## How it works

```
Text message -> MiniLM-L6-v2 -> Shape library -> Modified A* -> MuJoCo feasibility check -> Servo chain
```

1. **Natural language input.** A user sends a text message. A `MiniLM-L6-v2` embedding model matches the request to the closest shape in a pre-built library.
2. **Shape library.** We generated thousands of random shapes, then filtered them through an LLM to keep only the ones that resemble common objects and symbols.
3. **Path planning.** Each approved shape is deconstructed into a sequence of joint rotations using a modified A* search that accounts for servo torque limits and cantilever loading.
4. **Simulation.** Every candidate fold is feasibility-tested in MuJoCo against an accurate URDF with real physics (gravity, friction, collision, torque) before it ever reaches hardware. Each check takes roughly 25 ms.
5. **Execution.** The validated joint sequence is sent over a single serial bus to the robot, which morphs into the target shape. A typical fold is a 16-move sequence that completes in about 20 seconds.

## Hardware

- **17 custom rotation modules**, each an 80 mm cube
- **4:1 planetary gearbox** built into every module
- **Feetech STS3215** bus servos communicating over UART on a single serial bus
- **Node-style power distribution**, with servo driver boards fed at intervals along the chain to keep current stable during simultaneous moves

## Software stack

| Area | Tools |
| --- | --- |
| Language understanding | MiniLM-L6-v2 |
| Shape generation and filtering | Random shape generation, LLM-based filtering |
| Path planning | Modified A* (torque and cantilever aware) |
| Simulation | MuJoCo, URDF |
| Servo control | Feetech SDK, Python |

## Engineering notes

- Servo torque limits were calculated against the 4:1 gearbox to prevent chassis flex and gear unmeshing.
- Because every fold is simulated first, infeasible or unstable configurations are rejected offline instead of being discovered on hardware.
- All 17 servos were commissioned on one bus, including ID assignment, zeroing, and stall-safe torque release.

## Team

Built in one weekend by:

- Aydan Ling
- Justin
- Daniel
- Jerry

## Acknowledgements

Thanks to Hack the North for organizing the event and the showcase.
