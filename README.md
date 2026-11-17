# 3-Stage Pipelined RISC-V Core (RV32I)

A 32-bit RISC-V processor (RV32I) written in Chisel, synthesised with Vivado and running on a Digilent Zybo Z7-10 board (Xilinx Zynq-7000 SoC). The core runs a Space Invaders game written in C in real time, with VGA output.

Built as part of the course "Fondements matériels du numérique" (digital hardware foundations) at Ensimag, Grenoble INP.

## Architecture

![Microarchitecture diagram](docs/schema.jpg)

- **ISA**: RV32I (base integer instruction set)
- **Pipeline**: 3 stages, matched to the one-cycle read latency of the FPGA block RAM
  1. **Fetch**: instruction read from instruction memory
  2. **Decode / Execute / Memory**: register file read, ALU, branch resolution, memory request for loads and stores
  3. **Write-back**: load data formatting and register file write
- **Data hazards**: a forwarding unit sends the value being written back directly to the ALU inputs, so read-after-write hazards are resolved without stalls, including right after a load
- **Control hazards**: static branch prediction (assumed not taken); when a branch or jump is taken, the instruction fetched behind it is flushed
- **Memory**: dual-port block RAM, with separate ports for instructions and data

## Tools

- Scala 2.13 / Chisel 3, built with Mill
- Verilator and GTKWave for simulation
- Xilinx Vivado 2019.1 for synthesis and place and route
- Digilent Zybo Z7-10 (Xilinx Zynq-7000 SoC)

## Demo

### Space Invaders on the FPGA

![Space Invaders running on the board](docs/invaders.gif)

### Simulation waveforms

![GTKWave waveforms](docs/gtkwave.png)

Pipeline, hazard resolution and a taken conditional branch (`beq`).

## Running it

### Simulation

Tests are RISC-V assembly programs in `bench/`. Each one lists the values expected in register x31, and the test harness checks every write to x31 against them.

Run all tests:

```bash
make autotest
```

![Test output](docs/autotest_output.png)

Run one test:

```bash
make autotest PROG=instr_jalr
```

Run a simulation without checking results, to inspect the waveforms in GTKWave:

```bash
make simulation PROG=instr_jalr
```

### FPGA

```bash
make fpga PROG=invaders
```

Synthesis takes a few minutes, then the bitstream is loaded onto the Zybo board and the program starts on the VGA display and LEDs.

## Authors

- Loïs Bouley ([LinkedIn](https://www.linkedin.com/in/lois-bouley))
- Marwa El Khazrouni ([LinkedIn](https://www.linkedin.com/in/marwa-el-khazrouni/))
