# NASSCOM-VSD-SOC-Design-Program

#### Open-Source RTL to GDSII Implementation using OpenLane

## Sky130 Day 1 - Introduction to Open-Source EDA, OpenLANE, and Sky130 PDK

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




## Sky130 Day 2 - Good floorplan vs bad floorplan and introduction to library cells

A concise overview of the theory and process involved in floorplanning during the RTL-to-GDSII flow of a VLSI chip design.



#### Floorplanning:

Floorplanning is an important step in the physical design flow where the positions of blocks (standard cells, macros, and IPs) are determined within the core area of the die. An optimized floorplan improves performance, reduces area and power, and avoids congestion.



##### Core vs. Die

- **Die**: The complete silicon chip area.
- **Core**: The central area inside the die where logic is placed. The region between the die boundary and core is used for routing and I/O cells.



##### Utilization Factor

The utilization factor determines how much of the core area is filled with standard cells:
```
Utilization = Area of Standard Cells / Total Core Area
```

- High utilization: Smaller area, possible congestion.
- Low utilization: Easier routing, but wasted space.



##### Aspect Ratio

Aspect Ratio is defined as:
```
Aspect Ratio = Height / Width of the core
```

- Ideal value is close to 1 (square) for uniform routing and predictable performance.



##### Pre-Placed Cells

Certain blocks like memories or analog IPs are manually positioned before running automated placement tools. These are called **pre-placed cells** and help optimize the performance and integration of critical blocks.



##### Decoupling Capacitors (Decaps)

When logic switches from '0' to '1', it needs charge from the power supply. But due to resistance in wires, there can be voltage drops. If the drop is greater than the noise margin, switching fails.

**Solution**: Place decoupling capacitors near sub-blocks. These act as local charge reservoirs and stabilize the power supply.



##### Power Planning

Power planning ensures efficient and stable power distribution through:
- Power rings
- Power stripes
- Mesh/grid networks

This is vital to prevent IR drop and to ensure all parts of the chip receive adequate power.



##### Pin Placement

Pins are placed between the die and core boundary. Key points:
- Located close to the logic blocks they interact with
- Clock pins are wider to offer low-resistance paths and minimize delay



##### Logical Cell Placement Blockage

Certain regions like pin placement areas are blocked from automated cell placement. This avoids congestion and ensures reliable routing.

#### Lab: SKY130_D2_SK1 - Chip FLoor planning consideration
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

##### Placement

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

##### Cell Design FLow
![image](https://github.com/user-attachments/assets/198a1e26-62ce-44b7-8450-c1be8535012d)


## Sky130 Day 3 -  Design library cell using Magic Layout and ngspice characterization

we have to clone the inverter standard cell design from the github reposotory

```sh
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp ../../pdks/sky130A/libs.tech/magic/sky130A.tech ./

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
![image](https://github.com/user-attachments/assets/c8b235d0-e4d8-485e-b27c-2de80ab6c19b)


![image](https://github.com/user-attachments/assets/7f2c69d1-e012-4f90-9a79-a53dbdd7480d)

![image](https://github.com/user-attachments/assets/9e4ee921-a7a7-4056-85d2-eab191ce1c6c)

Spice extraction of inverter in magic.
```sh
# Extraction command to extract to .ext format
extract all
ext2spice cthresh 0 rthresh 0
# Converting to ext to spice
ext2spice
```
![image](https://github.com/user-attachments/assets/746e4013-4eeb-49eb-ae23-66a4a495a885)

![image](https://github.com/user-attachments/assets/338d61c5-7160-4e45-8b4a-d2f76ee61cc3)

oprn the spice file

![image](https://github.com/user-attachments/assets/a5261001-e46f-4f44-b014-04f7fb60822a)

Edit the spice file

run the command
```sh
ngspice sky130_inv.spice
```
![image](https://github.com/user-attachments/assets/1635a2d3-8016-4771-8996-4975ce4c78e1)

use this command to see the plot of output vs input
```sh
plot y vs time a
```

![image](https://github.com/user-attachments/assets/149eb352-6f64-4305-adbe-f1279b7b6db7)

###### Rise time calculation
![image](https://github.com/user-attachments/assets/8f2f765f-3bd9-485a-b3ca-0bd23af6b2a0)

Rise time=Time taken for output to rise to 80%−Time taken for output to rise to 20%

20% of output=0.66 V

80% of output=2.64 V

Rise transition time=2.24577−2.18214=0.06363 ns=63.63 ps

###### Fall time calculation
![image](https://github.com/user-attachments/assets/f2aac162-04c2-4492-8fd3-75dddb0307bf)

Fall time=Time taken for output to fall to 20%−Time taken for output to fall to 80%

20% of output=660 mV

80% of output=2.64 V

Fall time=8.068−8.040=0.028 ns


###### Propogation delay rise

![image](https://github.com/user-attachments/assets/43eed9dc-fbb7-4f8c-abba-91e5c6165bda)

Propagation Delay=Time taken for output to rise to 50%−Time taken for input to fall to 50%

50% of 3.3 V=1.65 V

Propagation Delay=2.185−2.15=0.035 ns


###### Propogation delay fall

![image](https://github.com/user-attachments/assets/db16ddbe-bd4a-4fc2-9f8a-f2ac5b4f8d4a)

Propogation delay Fall = Time taken for output to fall to 50%−Time taken for input to rise to 50%

50% of 3.3 V = 1.65 V

Propogation delay Fall = 4.05467−4.04998=0.00469 ns

###  Find problem in the DRC section of the old magic tech file for the skywater process and fix them.

```sh #Download the drc_test folder in home directory
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

#To extract the labs from the zip file
tar xfz drc_tests.tgz

# Change directory into the lab folder
cd drc_tests

# List all files and directories present in the current directory
ls -lrt

# Command to view .magicrc file
gvim .magicrc

#To open magic tool
magic -d XR
```

![image](https://github.com/user-attachments/assets/e29f6074-7af1-4733-8f0b-6566ce65b9b6)

![image](https://github.com/user-attachments/assets/44a65740-76d4-40d0-bc4f-8c60c4af3217)

![image](https://github.com/user-attachments/assets/bb18df93-ccd2-44f4-832f-9d190ebfcee9)

## Sky 130 Day4 - Pre-layout timing analysis and importance of good clock tree


tracks file

![image](https://github.com/user-attachments/assets/18077c00-dd4d-4ce6-8e99-5d6d7bd16d6b)


![image](https://github.com/user-attachments/assets/ab4bac6b-af48-4a74-838b-c8b26ebd1c7c)

###### checking all the requirements of the layout: 


Commands to open the custom inverter layout
```sh
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
![image](https://github.com/user-attachments/assets/9df7bc20-3bd9-47f3-9ce4-7fef1ff4417c)


![image](https://github.com/user-attachments/assets/04b70e6c-5b97-4702-b555-e19f90762e45)


![image](https://github.com/user-attachments/assets/4dc66a68-dcde-4d75-9844-c63a34869bbf)

```sh
#to write lef file
lef write
```


![image](https://github.com/user-attachments/assets/f9ffbb69-069e-4054-b296-99fc11f6a71a)

![image](https://github.com/user-attachments/assets/cd611484-4b4f-4653-ab08-ced8ab3d9bdc)

open lef file

![image](https://github.com/user-attachments/assets/a1889d8a-1bf6-400b-9495-32ab40aa4223)

![image](https://github.com/user-attachments/assets/83ef35a9-eb7e-4a1f-b0a9-a6b036266fbd)

```sh
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```
editing the config.tcl file

![image](https://github.com/user-attachments/assets/f24e7681-adaf-4bbb-8e37-76a357d43685)


Run openlane flow synthesis with newly inserted custom inverter cell.
```sh
# Open the openlane terminal
cd Desktop/work/tools/openlane_working_dir/openlane

docker

./flow.tcl -interactive

# Inside openlane terminal

# below cmd to be used to overwrite previous run data 
prep -design picorv32a -tag 06-04_20-28 -overwrite 

# Cmds to include the new cell led in merged.lef in /tm[ folder
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

run_synthesis
```

![image](https://github.com/user-attachments/assets/93713bfa-2616-478f-8402-5ee8dbd68aef)

![image](https://github.com/user-attachments/assets/ab299674-68c3-4d5d-8442-56f24a562a58)


the timing report shows that the slack has not been met meaning theres timing violation
![image](https://github.com/user-attachments/assets/8fa36eab-e40a-4c48-a7a2-39bfa7ee539d)










