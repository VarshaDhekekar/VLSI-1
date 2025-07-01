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

![image after zooming in](https://github.com/user-attachments/assets/ec67e5fb-84b4-47af-ba95-4fa598098c33)

The area highlighted by white boundary are the decap cells

We can select an object by moving the **+** cursor over the object and pressing **s** key. Select an input pin and type ***what*** command in the tkcon  window. It shows the information about that pin

![image](https://github.com/user-attachments/assets/97b3119a-9a0c-46fc-80ba-1470da52d2e0)

As you can see from the image, the pin is at metal 3. Peforming the step for getting details about a vertical pin:

![image](https://github.com/user-attachments/assets/a5bfd4ec-9204-45c2-afc8-be55bc5df05b)

The white boundary pin is the selected one and by typing the ***what*** command in the tkcon window you can see the details about it. It is in metal 2.

After zooming in the lower area, you will be able to see vertical boxes with congested text:

![image](https://github.com/user-attachments/assets/512a9676-088b-483d-b5dd-817bb0acf2e4)

- Black boxes with text	: Standard cells (logic gates, flip-flops)
- White rectangles	: Cell/block boundary or site
- Horizontal lines	: Routed metal layers (typically M1)
- Pink columns with blue text	: IO pins placed during floorplanning
- Dense alignment :	Indicates digital standard cell row placement

High Cell Density (close text) are standard cells like sky130_fd_sc_hd__* are tightly packed in rows.Each cell instance has a label, and when zoomed out, all these labels overlap.



## Library binding and placement

### Congestion aware placement using replace

Currently, our main goal is not timing, it is congestion. Therefore, the focus is on minimizing congestion during placement.
The placement process in OpenLANE happens in two stages: 
Global Placement and Detailed Placement. During Global Placement, cell legalization is not performed; it focuses mainly on optimizing the wirelength to reduce routing complexity and congestion. 
Detailed Placement follows Global Placement and ensures legalization, meaning all standard cells are placed within defined site boundaries without overlap.
When we run the placement step, Global Placement is executed first with the main objective of minimizing total wirelength.
After placement, we open the layout in Magic to visualize the actual standard cell placement. The view shown below illustrates how the cells are distributed and how the placement has been handled in terms of congestion and alignment.

First run this command :

![image](https://github.com/user-attachments/assets/b1f19c48-b09c-4b17-9b8a-0a517ef1ec82)

Magic layout opens and you can see the actual standard cell placement:

![image](https://github.com/user-attachments/assets/7eecda5a-0075-459e-9de2-2dae8a191844)

Zomm into the layout and you can see the flip flops, gates etc as shown in the iamge:

![image](https://github.com/user-attachments/assets/2c0538b2-4e9c-4ad2-b588-43cb4b3142a5)

The cells that we can see in this zoomed-in Magic layout view are the same standard cells that appeared congested in the earlier, in those lower vertical boxes.





# Day 3 - Design library cell using Magic Layout and ngspice characterization

## Labs for CMOS inverter ngspice simulations

### IO placer revision

So far, we have completed the floorplanning and placement stages. But if we want to modify the floorplan—for instance, to change the placement of pins (which are currently spaced equally)—we can do so using the set command.
We need to examine the available configuration switches. From them, we can locate the variable env(FP_IO_MODE). By default, this is set to 1, which places the pins at equal distances. To customize the pin placement, we change its value to 2 using:
***set ::env(FP_IO_MODE) 2***
After updating this setting, we must rerun the floorplanning step to apply the new pin configuration. Once completed, open the layout in Magic using the magic -T command to visually inspect the changes in pin positions.

![image](https://github.com/user-attachments/assets/c70f7100-4c9d-4e0b-b8b1-52fc100d30b8)

After running the layout again, we can see that the cells are now stacked on top of each other. Before they were equidistant

### Lab steps to git clone vsdstdcelldesign

Clone the github repository using the command ***git clone add_the_address_of_the_repo***

![image](https://github.com/user-attachments/assets/08c2e03e-7652-4fd1-ac26-84f7aa4f8173)

After that you can see the vsdstdcelldesign folder using the command ***ls -ltr***. Go to that folder and see the files present there. You will be able to see the .mag file

![image](https://github.com/user-attachments/assets/bb17a76a-d114-4bd4-9e76-fc1826a37f03)

Before opening the mag file, we need the tech file, so we have to copy the file from its present location to this address (of the mag file). You can see the command for the same in the below image:

![image](https://github.com/user-attachments/assets/0cabe5c6-7a57-4358-81d6-df65c9a53bb4)

Now, opening the magic layout becomes easy because we have both files at the same location and it is not required to give full address of the files. Now, run the below command:

![image](https://github.com/user-attachments/assets/fc556c46-0035-455b-855e-4285532aab35)

The magic layout opens and we can see the CMOS inverter layout:

![image](https://github.com/user-attachments/assets/118c2b56-d6cb-44a0-90e9-cd85adcd9c1b)


## Inception of layout ̂: A CMOS faabrication process
### Lab introduction to Sky130 basic layers layout and LEF using inverter

In this layout, you will observe that each colour represents different layer or component
- Blue-purple:	Local Interconnect (LI)	: First metal layer for short, local routing
- Light purple:	Metal 1 (M1) :	First global metal routing layer
- Pink	Metal : 2 (M2):	Second global metal routing layer
- Solid dashed: line	N-Well :	Region for placing PMOS transistors
- Green:	N-Diffusion	: Region for NMOS source/drain diffusion
- Brown:	P-Diffusion	: Region for PMOS source/drain diffusion
- Red:	Polysilicon Gate :	Gate material over the channel in transistors

You can view the component name by first moving the **'+'** cursor over that component and pressing **s** key, followed by typing **what** command in the tkcon window

![image](https://github.com/user-attachments/assets/234dfe2d-182e-445f-ae85-0a2cbceadf1a)

You can see in the above image that the yellow boundary component is selected and is identified as **nmos**
Now we can check connections too. For instance, we will move the cursor over the **Y** portion in the layout and press **s** 3 times. For the first press of **s**, the area gets selected and in the second press, 
the entire thing to which the highlighted portion is connected to gets selected.
Observe the below image:

![image](https://github.com/user-attachments/assets/b18a087b-ecb9-4134-9390-bbdb12f3da05)

**Observation**: We can observe that the area **Y** is connected to drains of PMOS and NMOS
Now we will check if the source of PMOS is connected to vdd and the source of NMOS is connected to GROUND or not following the exact steps as before.

Observe the below image:

![image](https://github.com/user-attachments/assets/328ec820-666c-42e4-9f4f-ef316b8bd86a)

**Observation**: We can see that source of PMOS is connected to the VDD line (white highlighted area)

Observe the below image:

![image](https://github.com/user-attachments/assets/9da16356-662c-42fa-942c-cfbae8ef33f4)

**Observation**: We can see that source of NMOS is connected to the GROUND (white highlighted area)

### Lab steps to create std cell layout and extract spice netlist
NGspice simulation:
To extract the layout on spice, use the command ***extract all***

![image](https://github.com/user-attachments/assets/bc86329c-f754-4c82-9e76-8023af529549)

Then check if the new file is added or not:

![image](https://github.com/user-attachments/assets/53af9dc2-5f09-4586-ab93-2ec8c0f23bcc)

As we can see, the file **sky130_inv.ext** is added.

Now, we will use the file created to create spice file to be used with NG spice using command ***ext2spice cthresh rthresh 0*** and ***ext2spice*** in the tkcon window.

![image](https://github.com/user-attachments/assets/6e8637b9-b7bc-45d6-9017-e6c3646040b3)

Check the spice file using the command ***vim sky_130inv.spice***. The contents of the file are as follows:

![image](https://github.com/user-attachments/assets/f0c8d08a-7508-44e6-8c19-a9dd33b99bc0)


## Sky130 Tech File Labs
### Lab steps to create final SPICE deck using Sky130 tech
Check the dimensions of the grid box in the layout by selecting the grid box first and typing ***box*** command in the tkcon window:

![image](https://github.com/user-attachments/assets/3dff595a-1929-42bc-a72c-bc03cd90e5a0)

We need to update the **sky130_inv.spice** file by setting the scale to 0.01, including the pshort and nshort.lib files (in the 'libs' folder inside 'vsdstdcelldesign' folder), setting the supply voltage "VDD"
to 3.3v by VDD VPWR 0 3.3V command. and similarly setting the value of VSS too. Now, we need to specify the input files by Va A VGND PULSE(0V 3.3V 0 0.1ns 2ns 4ns).
Also adding the commands for the analysis like, .tran 1n 20n, .control , run,.endc,.end. The changes made are shown below:

![image](https://github.com/user-attachments/assets/ec13d867-7315-4e80-b53d-051b6352ab92)

After running the file using command ***ngspice sky130_inv.spice*** we get the following output:

![image](https://github.com/user-attachments/assets/7f870699-9503-43ef-8d73-b51bdc15057d)

We will plot the graph using command ***plot y vs time a*** command

![image](https://github.com/user-attachments/assets/78daf2e4-932a-4899-a608-3eb4cb471dbc)

The graph looks like this:

![image](https://github.com/user-attachments/assets/080d73ea-2e91-4526-a7c5-39f59003334c)

To make the graph more smoother, we can increse the C3 parameter from 0.024fF to 2fF. This is the output:

![image](https://github.com/user-attachments/assets/26dea36c-5cde-4819-ad5e-123e87fa111d)


### Lab steps to characterize inverter using sky130 model files

Our objective is to find the values of 4 parameters namely:
- rise time
- fall time
- propagation delay
- cell fall delay

Finding rise time:
Rise time is the time it takes for a signal (usually a voltage signal) to transition from a low value to a high value. Specifically:Rise time is defined as the time taken for the output waveform to 
rise from 20% to 80% of its final value. The beginning (0%) and end (100%) of transitions often consist of noise, ringing, or non-linear behavior.
Measuring from 20% to 80% gives a more stable and accurate estimate of how fast the signal is transitioning.

![image](https://github.com/user-attachments/assets/8b738c76-a30a-48e7-a263-32f03a7104d5)

rise time= (2.2489 - 2.1819)e-09 = 66.92 psec

Finding fall time:
Fall time is the amount of time it takes for a digital signal to transition from a high value to a low value. Specifically: Fall time is defined as the time taken for the output waveform to drop from 
80% to 20% of its final high value.

![image](https://github.com/user-attachments/assets/12aecc00-6702-4f6a-8357-66a9f6079fc7)

fall time= (4.09512 - 4.05264)e-09 = 42.51 psec

Finding propagation delay:
Time difference between 50% input and 50% output

![image](https://github.com/user-attachments/assets/ef5462d6-f362-4dbd-8d43-a5b1215d58d3)

propogation delay =(2.2106 - 2.15012)e-09 = 60.48 psec

Finding cell fall delay:
Cell Fall Delay is the time difference between the input signal reaching 50% of its rising transition and the output signal reaching 50% of its falling transition.

![image](https://github.com/user-attachments/assets/4a78b24c-4e63-4740-b72a-cc87f0384c43)

cell fall delay =(4.07735 - 4.04988)e-09 = 27.47 psec.

With these steps, our inverter has been characterized.


### Lab introduction to Sky130 pdk's and steps to download labs

First download the lab files required for doing the DRC corrections using the command **wget website_URL** and extract the files using **tar xfz drc_tests.tgz**
Go to drc_tests folder and run the command **ls -al** to see all directories. You will be able to see **magicrc file** using the command ***gvim .magicrc***

![image](https://github.com/user-attachments/assets/cbe612fc-97bb-4759-b153-9cdb27d3ff92)

Below is the content of .magicrc file:

![image](https://github.com/user-attachments/assets/98a669c2-5253-4179-906b-86349158a3ca)


### Lab introduction to Magic and steps to load Sky130 tech-rules

open the magic layout using ***magic -d XR &*** and open a Met3 file. The layout now looks like this:

![image](https://github.com/user-attachments/assets/2d01bdd6-60d3-4275-836a-f0f38dfba6e6)

Observe the different layouts with different DRC values, called rule numbers. These rule number can be found at the google-skywater documentation. The link is:
*skywater-pdk.readthedocs.io/en/main/rules/periphery.html#m3*

Now select any layout area and type ***drc why*** in the tkcon window:

![image](https://github.com/user-attachments/assets/00fdc6db-96e2-4faa-99c9-83444d460c17)

Now move the cursor over metal3 icon and press **p** button and type ***pek*** in the tkcon window. After that, type ***cif see VIA2***. You will observe black squares within the layout

![image](https://github.com/user-attachments/assets/b97ef6a2-6d9a-4bcd-a3c8-935f429036b5)

Now type the ***load poly.mag*** command in tkcon window. This loads the poly.mag file

![image](https://github.com/user-attachments/assets/05f9e2c2-7d60-4635-9071-3014a1da66e0)

Select poly.9 and check for the same using the documentation from the link provided before.

![image](https://github.com/user-attachments/assets/597e6337-fa4c-4dff-b3f7-e025f3376782)

To find the error we can look up the sky130_inv.tech file. Make changes accordingly and save

![image](https://github.com/user-attachments/assets/03f1dc9c-4f4c-4836-bf9e-c8fbf072e452)

Next step is to type the comman ***tech load sky130_inv.tech*** in tkcon window and then ***drc check***

![image](https://github.com/user-attachments/assets/6b16633f-7d2a-4422-9114-a350e7d193e2)














































































































