# DAY 1: Inception of open-source EDA, OpenLANE and sky130 PDK

# Design preparation step
To start Openlane, navigate to the openlane directory using the command ***cd Desktop/work/tools/openlane_working_dir/openlane*** and run the command ***docker*** 
.Now type the command: ***./flow.tcl -interactive***. This will start openlane.

When you enter OpenLANE, you're entering a design automation environment that helps you go from RTL (Register Transfer Level) to GDSII (final chip layout).

flow.tcl — Full Automation
This script is the main flow controller of OpenLANE.
As the name implies, it "goes with the flow", executing all design steps:
-Synthesis
-Floorplanning
-Placement
-Clock tree synthesis (CTS)
-Routing
-DRC/LVS checks
-GDSII generation

Interactive mode in OpenLANE is essential when you want more control, flexibility, and visibility over each stage of the ASIC design flow.
Now we need to load all packages which are needed to run the flow using command: ***package require openlane 0.9***

![starting openlane](https://github.com/user-attachments/assets/d1ed0b27-04a7-4b95-b909-8fee752298d5)

In openlane, few designs are in-built. We'll use the design picorv32a. Go into the directory picorv32a using command: ***cd designs/picorv32a*** and run the command ***ls -ltr***. You'll see many files. Open *config.tcl* file using the command ***less config.tcl***. This config.tlc file contains every detail about the design for example: details about enrollment, clock period, clock period port etc.

![config.tcl file](https://github.com/user-attachments/assets/cdb182af-6f7a-4d7e-9772-1544d9e3e28c)

The next step is to run synthesis. Before that we will have to run the ***prep -design picorv32a*** command. This command sets the stage for the RTL-to-GDSII flow by organizing the necessary files and configurations, allowing the user to proceed with each design step interactively, such as synthesis, floorplanning, placement, routing, and final GDSII generation.

![design prep](https://github.com/user-attachments/assets/bddc7fe2-946c-437b-85f6-3e04f179b5f2)

You can see at the end in the image that the preparation is complete


#Review files after design prep and run synthesis














