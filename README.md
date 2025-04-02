# NASSCOM-VSD-SOC-Design-Program

## Open-Source RTL to GDSII Implementation using OpenLane

### Sky130 Day 1 - Introduction to Open-Source EDA, OpenLANE, and Sky130 PDK

### Lab: SKY130_D1_SK3 - Getting Familiar with Open-Source EDA Tools

#### Workspace Setup
All OpenLane runs must be executed from the following directory:
```sh
~/Desktop/work/tools/openlane_working_dir/openlane
```

#### Design Information
The design we will be working on is `picorv32a`. The directory containing all design data, including `picorv32a`, is:
```sh
~/Desktop/work/tools/openlane_working_dir/openlane/design
```
Each design has a `config.tcl` file, which takes the highest priority. Any commands specified in this file override the default commands in the OpenLane tools.

#### Steps to Open OpenLane Instance
1. Start Docker:
   ```sh
   docker
   ```
2. Open OpenLane in interactive mode:
   ```sh
   ./flow.tcl -interactive
   ```
3. Load OpenLane version 0.9:
   ```sh
   package require openlane 0.9
   ```
4. Prepare the `picorv32a` design for implementation:
   ```sh
   prep -design picorv32a
   ```

#### Running Synthesis
Once OpenLane is set up, synthesis can be run using:
```sh
run_synthesis
```

These steps must be followed each time OpenLane is launched to work on the `picorv32a` design.

