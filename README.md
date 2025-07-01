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
               Number of D flip flops/Number of cells

So we get 10.84%

Prior to running the synthesis step, the result folder was empty but now we can view the results by navigating into the results folder and viewing the **picorv32a.synthesis.v** file as shown in the image

![viewing synthesis results](https://github.com/user-attachments/assets/dd5d1520-06e4-4a78-879a-3620c1d5d1f6)

The file has the following content (mapping done by ABC):

![image](https://github.com/user-attachments/assets/32569960-3f03-416b-9ed6-5bb988f0c4f9)

We can view the stats report using the command ***less 1-yosys_4.stat.rpt***

![stats report](https://github.com/user-attachments/assets/0ea979e2-cf6d-40d9-b731-75f049a371c4)

You can also view the static timing analysis **(STA)** report. This is part of OpenSTA’s output (used by OpenLANE internally) and specifically displays a worst-case (max) timing path for the clock signal
Each entry in the report provides detailed information on signal transitions, delays, and cumulative arrival times through various gates and nets. This analysis is crucial for identifying setup time violations and ensuring the design is timing-safe before proceeding to physical implementation.

![STA](https://github.com/user-attachments/assets/5c5a73ac-0e37-4ab4-ac86-2025f9a3a4fc)




# Day 2 - Good floor planning considerations
## Chipfloor planning considerations
Before going for floorplanning, we need some information which we can see from README.md in configuration directory

![image](https://github.com/user-attachments/assets/4d1f1b12-c07d-4218-8c3a-f7bdb9cc723b)

You can view the configurations in the README.md using ***less README.md*** command

![README.md](https://github.com/user-attachments/assets/3f33e90c-942c-4b55-90ec-63a84ee1913e)

Here we can see that the core utilization ratio is 50% and aspect ratio is 1. similarly other information is given too. But it is not neccessory to take these values. we need to change these value as per the given requirments. The values which you see are default values.

Here FP_PDN files are set the power distribution network. These switches are set in the floorplane stage bydefault in OpenLANE.
The default configurations can be viewed in floorplan.tcl file

![floorplan.tcl file](https://github.com/user-attachments/assets/06bb2743-be67-4d95-b0b8-940a036bf3b9)

### Next we will review files and run floorplan:
Run the floorplan using ***run_floorplan*** command.
The run_floorplan command performs the floorplanning stage, which includes:
- Die and core area sizing
- IO pin placement
- Macro placement (if any)
- Power grid setup (initial)
- Cell placement boundaries
In the run folder, you will find the config.tcl file, which contains all the configuration settings used during the current OpenLANE flow. By opening this file, you can view the specific parameters and environment variables that were applied, providing a clear overview of the design constraints and flow options accepted and executed in the current run.

![image](https://github.com/user-attachments/assets/bf01b0bf-1092-422d-ba54-834d5f338cfe)


After running floorplan, also view the floorplan.tcl file

![image](https://github.com/user-attachments/assets/b77fe430-5426-469f-9cea-1ba263c4b727)

In floorplan directory, there is also another file called *picorv32a.floorplan.def*
Here you can see the die area

![image](https://github.com/user-attachments/assets/5ed816b2-b105-40b3-97f0-0ff4f94c7f33)

The die area is specified as (0 0) to (660685 671405), and the unit distance is defined as 1000 database units per micron. This means that 1 micron is equivalent to 1000 database units. Therefore, the values 660685 and 671405 are in database units, and by dividing them by 1000, we obtain the actual chip dimensions in micrometers.
Hence the width of chip is 660.685 micrometer and height of the chip is 671.405 micrometer.

To see the layout of the flow, we can do so using magic. The command for opening magic is as below:

![image](https://github.com/user-attachments/assets/09c06bef-725b-498f-b85a-15dc6eff1a2e)

After magic opens, we can see the below layout:

![image](https://github.com/user-attachments/assets/c760e738-ff35-47e4-80c6-1a40402cb5f7)

After zooming in using the **z** key, you can observe that the input and output pins are equidistant. You can zoom out using **shift + z**
We can select an object by moving the **+** cursor over the object and pressing **s** key. Select an input pin and type ***what*** command in the tkcon  window. It shows the information about that pin

![image](https://github.com/user-attachments/assets/97b3119a-9a0c-46fc-80ba-1470da52d2e0)

As you can see from the image, the pin is at metal 3. Peforming the step for getting details about a vertical pin:

![image](https://github.com/user-attachments/assets/a5bfd4ec-9204-45c2-afc8-be55bc5df05b)

The white boundary pin is the selected one and by typing the ***what*** command in the tkcon window you can see the details about it. It is in metal 2.













































