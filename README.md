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

we see this once the synthesis is run properly.
![image](https://github.com/user-attachments/assets/daac4b54-1f9e-49f9-b277-a6c8808e5d0d)

the reports and results will be present in the following directaries. these dicrectories will be populated as the run progresses.
reports:
![image](https://github.com/user-attachments/assets/9bac6b7d-e37f-439a-af79-fe07e9f8ae9b)

results:
![image](https://github.com/user-attachments/assets/9a5329b5-b7fb-45ca-bee8-8ef2761d3cf4)

Task: to calculate the DFF ratio
checking for latest file we have reports/synthesis/1-yosys_4.stat.rpt from dir image above. Therefore Flip Flop ratio is:

![image](https://github.com/user-attachments/assets/00355ed0-b5ed-48f8-8598-a4e20756d58b)

(1623/14876) *100 = 10.84

![image](https://github.com/user-attachments/assets/c30e2dcf-6e65-43b2-b54b-b0d9451069ef)

we can also analyse the sta reports which are generated after this step.

### Sky130 Day 2 - Good floorplan vs bad floorplan and introduction to library cells

A concise overview of the theory and process involved in floorplanning during the RTL-to-GDSII flow of a VLSI chip design.



## Floorplanning:

Floorplanning is an important step in the physical design flow where the positions of blocks (standard cells, macros, and IPs) are determined within the core area of the die. An optimized floorplan improves performance, reduces area and power, and avoids congestion.



## Core vs. Die

- **Die**: The complete silicon chip area.
- **Core**: The central area inside the die where logic is placed. The region between the die boundary and core is used for routing and I/O cells.



## Utilization Factor

The utilization factor determines how much of the core area is filled with standard cells:
```
Utilization = Area of Standard Cells / Total Core Area
```

- High utilization: Smaller area, possible congestion.
- Low utilization: Easier routing, but wasted space.



## Aspect Ratio

Aspect Ratio is defined as:
```
Aspect Ratio = Height / Width of the core
```

- Ideal value is close to 1 (square) for uniform routing and predictable performance.



## Pre-Placed Cells

Certain blocks like memories or analog IPs are manually positioned before running automated placement tools. These are called **pre-placed cells** and help optimize the performance and integration of critical blocks.



## Decoupling Capacitors (Decaps)

When logic switches from '0' to '1', it needs charge from the power supply. But due to resistance in wires, there can be voltage drops. If the drop is greater than the noise margin, switching fails.

**Solution**: Place decoupling capacitors near sub-blocks. These act as local charge reservoirs and stabilize the power supply.



## Power Planning

Power planning ensures efficient and stable power distribution through:
- Power rings
- Power stripes
- Mesh/grid networks

This is vital to prevent IR drop and to ensure all parts of the chip receive adequate power.



## Pin Placement

Pins are placed between the die and core boundary. Key points:
- Located close to the logic blocks they interact with
- Clock pins are wider to offer low-resistance paths and minimize delay



## Logical Cell Placement Blockage

Certain regions like pin placement areas are blocked from automated cell placement. This avoids congestion and ensures reliable routing.

### Lab: SKY130_D2_SK1 - Chip FLoor planning consideration
```sh
   run_floorplan
   ```

![image](https://github.com/user-attachments/assets/0d09e8fa-6454-455a-b1c3-72346118734b)

results:
![image](https://github.com/user-attachments/assets/778bd31c-6f6c-463a-a871-138b4168d405)

![image](https://github.com/user-attachments/assets/207d0ada-b660-4904-bfe2-29d1cb5f339a)

picorv32a def file:

![image](https://github.com/user-attachments/assets/30a5c0f9-8854-4dc8-91d0-219406ec92d5)

According to the def file:

Unit Distance = 1 Micron

Width (in unit distance)  = 660685 - 0 = 660685

Height (in unit distance) = 671405 - 0 = 671405

Die Area (in microns²) = (660.685 × 671.405) = 443,587.21 microns²

This calculated area helps guide decisions around placement, routing, and chip scaling.

#command to load picorv32a.placement.def file in magic
```sh
   magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read tmp/merged.lef def read results/floorplan/picorv32a.floorplan.def &
   ```

![image](https://github.com/user-attachments/assets/6eb0c289-18ea-4930-8c4f-387eaeadab71)

to zoom in: first left click on the area of interest , here we'll go near io pins. and then right click creating a box and click z to zoom.

as you zoom in, hover the mouse on a io pin and click s, it'll select that pin.
now  open the tkcon window and type what and youll see the information of the selected pin as shown below


![image](https://github.com/user-attachments/assets/552bec5a-337e-4c50-b7be-cfa8d1b3ba9e)

##Placement

In OpenLane tehre are two placement stages,
1. Global Placement
2. Detailed Placement

```sh
   run_placement
   ```

![image](https://github.com/user-attachments/assets/4d2bca93-0a59-4e4c-ae86-02f63ef0de2e)

To see how placement is done,
```sh
   magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read tmp/merged.lef def read results/placement/picorv32a.placement.def &
   ```

![image](https://github.com/user-attachments/assets/0eee7f48-2297-42fc-af44-8eb368ea7864)


![image](https://github.com/user-attachments/assets/1e064d9f-9977-4b04-b788-eb2569f46b1b)







