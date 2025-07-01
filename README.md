# DAY 1: Inception of open-source EDA, OpenLANE and sky130 PDK

## Design preparation step
To start Openlane, navigate to the openlane directory using the command ***cd Desktop/work/tools/openlane_working_dir/openlane*** and run the command ***docker*** 
.Now type the command: ***./flow.tcl -interactive***. This will start openlane.

When you enter OpenLANE, you're entering a design automation environment that helps you go from RTL (Register Transfer Level) to GDSII (final chip layout).

flow.tcl — Full Automation
This script is the main flow controller of OpenLANE.
As the name implies, it "goes with the flow", executing all design steps:
- Synthesis
- Floorplanning
- Placement
- Clock tree synthesis (CTS)
- Routing
- DRC/LVS checks
- GDSII generation

Interactive mode in OpenLANE is essential when you want more control, flexibility, and visibility over each stage of the ASIC design flow.
Now we need to load all packages which are needed to run the flow using command: ***package require openlane 0.9***

![starting openlane](https://github.com/user-attachments/assets/d1ed0b27-04a7-4b95-b909-8fee752298d5)

In openlane, few designs are in-built. We'll use the design picorv32a. Go into the directory picorv32a using command: ***cd designs/picorv32a*** and run the command ***ls -ltr***. You'll see many files. Open *config.tcl* file using the command ***less config.tcl***. This config.tlc file contains every detail about the design for example: details about enrollment, clock period, clock period port etc.

![config.tcl file](https://github.com/user-attachments/assets/cdb182af-6f7a-4d7e-9772-1544d9e3e28c)

The next step is to run synthesis. Before that we will have to run the ***prep -design picorv32a*** command. This command sets the stage for the RTL-to-GDSII flow by organizing the necessary files and configurations, allowing the user to proceed with each design step interactively, such as synthesis, floorplanning, placement, routing, and final GDSII generation.

![design prep](https://github.com/user-attachments/assets/bddc7fe2-946c-437b-85f6-3e04f179b5f2)

You can see at the end in the image that the preparation is complete


## Review files after design prep and run synthesis

In the step of design preparation, a *merged.lef* file is created insode the *tmp* folder as you can see in the image below which you can open using ***less merged.lef*** in the appropriate directory. The merged.lef file is a combined LEF (Library Exchange Format) file generated during the OpenLANE flow. It includes:

- Technology LEF – describes design rules, routing layers, via layers, etc.

- Cell LEF – includes abstract layout info of standard cells and macros (pins, shapes, blockages, etc.)

![merged.lef file](https://github.com/user-attachments/assets/594a32dd-6da2-4c6f-9389-d3db398fc61a)

Now we will run the sysnthesis using ***run synthesis*** command in openlane 
![run synthesis command](https://github.com/user-attachments/assets/b03d3a72-9782-4e7a-be0d-000b4a6dcd7d)


## Steps to characterize synthesis results
From the results of the synthesis you can see that the number of cells are **14876** and the D flip flops are **1613**
![synthesis results](https://github.com/user-attachments/assets/fee7873e-4692-48ad-9856-29d387361861)

The flop ratio can be calculated using the formula:
               Number of D flip flops
               -----------------------
               Number of cells



















