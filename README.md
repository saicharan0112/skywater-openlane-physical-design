# Skywater-OpenLANE-physical-design

VSD SKy130 Openlane Physical Design is a 5-day workshop where I learnt about the opensource EDA tools involved in the synthesis, physical design and gdsii stages. 
First, I will be briefing about the Openlane flow and the tools involved in it. Later, I will documenting my journey of 5 days in the workshop.

## OpenLane 

OpenLane Flow is an automated RTL2GDSII flow which involves various opensource tools like OpenSTA, Yosys, Netgen, Magic etc. Below is the image illustration the flow along with
the tools involved. The main motto of this Openlane is to automate the RTL2toGDSII flow and producing clean GDSII files. "_OpenLANE is a tape-out-hardened flow that addresses two main use cases: hardening a macro and integrating
a System-on-a-Chip (SoC)._" - the research paper introducing OpenLANE (click [here](https://woset-workshop.github.io/PDFs/2020/a21.pdf) to view)

![OpenLane Flow - from official github repo](https://github.com/efabless/openlane/raw/master/docs/_static/openlane.flow.1.png "OpenLane Flow - from official github repo")

## Table of Contents

* [**Day-1 [Inception of open-source EDA, OpenLANE and Sky130 PDK]**](https://github.com/Lanka1919/skywater-openlane-physical-design#day-1-inception-of-open-source-eda-openlane-and-sky130-pdk)
  
  > [**1. Synthesis**]()
   
* [**Day-2 [Good floorplan vs bad floorplan and introduction to library cells]**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#day-2-good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)

  > [**2. Floorplanning**]()
 
  > [**3. Placement**]()
   
* [**Day-3 [Design library cell using Magic Layout and ngspice characterization]**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#day-3-design-library-cell-using-magic-layout-and-ngspice-characterization)
     
  > [**Working with a custom cell design (Here it was an inverter)**]()
   
* [**Day-4 [Pre-layout timing analysis and importance of good clock tree]**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#day-4-pre-layout-timing-analysis-and-importance-of-good-clock-tree)
      
  > [**4. CTS**]()
 
* [**Day-5 [Final steps for RTL2GDS using tritonRoute and openSTA]**](https://github.com/lankasaicharan/skywater-openlane-physical-design#day-5-final-steps-for-rtl2gds-using-tritonroute-and-opensta)
   
  > [**5. Routing**]()
  
  > [**6. RC Extraction**]()

-----------------

## Day-1 [Inception of open-source EDA, OpenLANE and Sky130 PDK]

The understanding of a chip started from the package and went till the macros inside a chip. Later, basic understanding of RISC-V ISA was given followed by a detailed understanding on how softwares interact with hardware and what are the intermediate components that are involved in the process. 

Moving forward, I was introduced to the OpenLane ASIC flow and the tools involved in it. In a nutshell, we get to give the RTL file (usually a .v file) and the PDK, in this case it is the skywater130 PDK, to this flow and the GDSII (clean) will be generated. OpenRoad application is the one where the PnR happens. Though for detailed routing, we need to move out of the OpenRoad application for using the TritonRoute (another tool) which is part of the flow. 

#### Invoking OpenLane and performing initial preparations

![Invoking OpenLane and performing initial preparations](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/invoking%20opelane_initial_settings.png "Invoking OpenLane and performing initial preparations")

### **1.Synthesis**

![Running Synthesis](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/running_synthesis.png "Running Synthesis")

#### Summary of cells in the design after optimization in the synthesis

![Optimized netlist summary](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/cells_summary_after_synthesis_and_optimization.png "Optimized netlist summary")

#### Slack Report of min and max paths of the clock

![min_path](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/slack_report_of_min_path.png "Min Path Slack Report")

![max_path](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/slack_report_of_min_path.png "Max Path Slack Report")

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Day-2 [Good floorplan vs bad floorplan and introduction to library cells]

A brief understanding on floorplan stage and the factors involved in it like core utilization, aspect ratio etc., was given. Later, I came across the method of loading the prev run file while preparing the openlane. While performing floorplan, I came across the precedency of various config files. Those files and the order of precedence is shown below.
### **2.Floorplanning**
#### Default floorplan config file
![default floorplan config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/floorplan_defaults_config_tcl_file.png "Default file")

#### config file
![config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/config_tcl_file.png "config file")

#### Sky130a config file
![Sky130a config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/skywater_openlane_config_tcl_file.png "Sky130a config file")

#### Precendency
**Sky130a config file > config file > default floorplan config file**


In floorplan, we fix the core utilization ratio, aspect ratio and provide decap cells, welltap cells. Later, we fix the I/O pins position. The floorplan was done using *run_floorplan* command and employed the **Magic** tool to view the layout by importing the def file generated. 

#### Floorplanning in OpenLANE
![Floorplan](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/floorplan.png "Floorplan")

#### Magic tool invoking by providing lef, tech library and def file
![Magic tool config](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/using_magic_toview_floorplan_def.png "Magic tool config")

#### Layout after floorplan stage in Magic tool
![Magic Tool Layout](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/floorplan_layout.png "")

#### Pins and decap cells
![Pins and Decap cells](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/pins_and_decap_cells.png "")

#### Welltap cells arranged in checker board fashion
![Welltap cells](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/welltap_cells_checkerboard.png "Welltap cells arranged in checker board fashion")

#### Location of Std cells in the floorplan
![Std cells](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/stdcells_at_the_bottom_of_floorplan_layout.png "Location of Std cells in the floorplan")

#### Std cells in the layout
![Std cells in the layout](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/std_cell_in_layout.png "Std cells in the layout")

### **3.Placement**

After performing floorplan, we place the standard cells (which were already present at the bottom left of the design) and observe the DRCs. We do this by giving the command *run_placement* in OpenLANE. Placement in OpenLANE occurs in two stages - Global placement and Detailed placement. In global placement, all cells are placed on the floorplan in a random manner and in detailed placement, the cells positions are legalized. Legalization in the sense, it makes sure that cells are not overlapping. In OpenLANE, placement happens by having a goal (for congestion driven) to reduce the HPWL (Half Parameter Wire Length), hence taking many iterations. We invoke the **Magic** tool to view the layout of the design after placement of std cells. 

#### Placement in OpenLANE
![Placement in OpenLANE](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/placement_in_openlane.png "Placement in OpenLANE")

#### Invoking Magic tool to view placement.def file
![Invoking MAGIC tool to view placement.def file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/invoking_magictool_for_placement.png "Invoking Magic tool to view placement.def file")

#### Layout of the design after Placement stage in Magic tool
![Layout of the design after Placement stage in Magic tool](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/placement_magictool.png "Layout of the design after Placement stage in Magic tool")

We generally know that there are two types of placements 
* Timing Driven
* Placement Driven

We can set the same in OpenLANE as well. Below is the config file in which the environment variable named *PL_TIME_DRIVEN* is set as **0** which indicates FALSE. Hence, the placement performed above is **Congestion Driven Placement** by default.

#### Timing Driven is set to FALSE
![Timing Driven is set to FALSE](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/timing_driven_is_set_false.png "Timing Driven is set to FALSE")

**The interesting thing to notice is that the PG routing is not done yet. Usually (in ASIC flow), we perform this during Floorplan stage itself. But in the OpenLANE flow, PG routing happens after the CTS and before Routing.**

------

## Day-3 [Design library cell using Magic Layout and ngspice characterization]

### 16-mask CMOS process
I found this interesting, hence noted it down while imagining each step in the process. 
Select the substrate
~40nm of SiO2
~80nm of Si3N4
~1um of photo-resist
Layouts -> masks are placed on photo-resist layer (acts as protection layer from photo-lithography process) - masks are used here
UV light will remove photo-resist
Remove the mask
etching Si3N4
Remove extra photo-resist  
oxidation growth on other areas by placing in oxidation furnace using a process called LOCOS (local oxidation of silicon
remove  extra Si3N4 using hot phosphoric acid

**N-well and P well formation**
-> Deposit photo-resist layer
-> place the mask according to the layout
-> UV light will remove the unmasked region
-> remove the mask
-> Deposit Boron atoms using Ion implantation process to create a P-well

By following similar process and by deposit phosphorous atoms to form N-well

Now take the complete material and place in high temp furnace to drive-in diffusion. After this process, N-well and P-well will diffuse deep into substrate diffusing half of the substrate). This process is called is twin-tub process.

**Formation of Gate**
-> Photo-resist material is deposited followed by the mask
-> Deposit boron on the p-well so that we achieve required doping concentration on the surface
Similarly, we need to do the process for n-well by using arsenic doping.
Now, we have n-doping and p-doping on the top layer of n-well and p-well. But in this whole process, the chances of damage to the SiO2 layer is inevitable. Hence, we etch this oxide layer using dilute hydrofluoric acid and regrow the oxide layer. All these will effect our threshold voltage.

Now, we grow a thick layer of poly-silicon oxide layer. Again , we deposit the photo-resist material and place mask. We remove the extra photo-resist layer and the masks as well. Now, remove the poly silicon layer outside the photo-resist. 
Remove the photo-resist material on the gate poly-silicon and at the end, we get the required gate.

Now, we have Gate. Next, we need to form the drain. 
Deposit the photo-resist material and place the mask on the m-well part. Remove the photo-lithography material and the mask as well. Dope the phosphorus on the p-well side. By this, we get the N-implant in the p-well. 
We repeat the same steps to deposit the lightly doped P-implant in the N-well. 
This is called as Lightly doped drain (LDD) formation. 
After this, we deposit the Si3N4 and use plasma an-isotropic etching process to remove this and form a side-wall  spacers at the gate. 
Now, we deposit a thin layer of screen oxide to avoid the channeling effect during implantation.

**Formation of source and drain**
-> We follow the similar process of protecting one side of the substrate (N-well) and dope arsenic on the N-well side. By this, we get more doping of N in the P-well but the less doping which is protected by the side-wall spacers is retained. 
-> We do the same process but use Boron for N-well. 
After this, we place this device in a high-temp furnace to let the P+ and N+ penetrate more into their respective wells. 
After this whole process, we get a doping configuration like P+P-N for N-well and N+N-P for P-well (+ indicated more doping, - indicates less doping)

Now, since our gate, source and drain regions are ready, we now build the contacts to control them.
We, first, etch the thin oxide using HF solution. We deposit titanium on wafer surface using sputtering process. Then, we heat the wafer in N2 atmosphere which results in the formation of TiS2 in-place of pure titanium. We also have TiN developed on the wafer which will be used for local communications.
Now, we do photo-lithography (placing photo-resist material and removing the same from unwanted areas using masks). After this we will be left out with the TiN which will be used for interconnects.
#### A view of CMOS Inverter 
![A view of CMOS Inverter](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/cmos%20middle%20step.png "")


**Higher level metal formation**
-> since the surface of the wafer (incomplete mosfet in this case) is uneven, we deposit a thick layer of SiO2 which is doped with phosphorous or boron. and level up the surface using a process called chemical mechanical polishing (CMP) technique. 
To develop the higher metal connections, we follow the photo-lithography process and use masks to make space at our required connection positions. 

After this, we deposit a series of materials and do photo-lithography and prepare 3 metal layers. After this, we drill holes to the terminals and get our connections to control our source, gate and drain  regions of the inverter created. If we carefully observe, we totally used 16 masks (the layouts) to build this device. Hence, this complete process is called as “16-mask CMOS process”

#### Final view of CMOS Inverter 
![Final view of CMOS Inverter](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/cmos%20last%20step.png "")


### Understanding DRC in MAGIC tool
To understand the MAGIC tool and its DRC engine, we use the inverter design mag file. For that, we clone the repo and run MAGIC tool using the **Skylane130A** tech file and **inverter** mag file.

#### Cloning the repo  for the inverter  
![Cloning the repo  for the inverter](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/cloning%20git%20repo.png "")
#### Layout of the inverter  
![Layout of the inverter](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/inverter%20layout.png "")
#### Layers menubar 
![Layers menubar ](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/metals.png "")
#### Connections with input port A  
![Connections with input port A](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/connections%20with%20input%20A.png "")
#### Verifying the PMOS source terminal is connected to VDD   
![Verifying the PMOS source terminal is connected to VDD](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/source%20of%20pmos%20connected%20to%20VDD.png "")
#### Verifying the NMOS source terminal is connected to VSS   
![Verifying the NMOS source terminal is connected to VSS](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/source%20of%20nmos%20connected%20to%20VSS.png "")
#### DRC dropdown menu
![DRC dropdown menu](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/drc%20window.png "")

To check the DRC engine, we manually create the DRC error. If we could observe the starting imported layout, we get DRC errors as **0**. Now, we delete the N-well and create the error. The interesting thing when compared with traditional verifications tools is the Dynamic DRC engine which means that DRC engine runs in background w.r.t changes in the layout. 

#### Creating DRC error   
![Creating DRC error](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/creating%20drc%20violation.png "")

To know the complete info about the DRC error, after selecting the drc error, shift to tkcon window of MAGIC tool and see the info as shown below.

#### Error info in the tkcon window   
![Error info in the tkcon window](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/drc%20error%20showing%20in%20tkcon%20window.png "")


### Library Characterization

Basically, by library characterization, we mean to identify 4 parameters. Rise transition delay, fall transition delay, rise cell delay and fall cell delay
We consider a basic inverter layout to identify the parameters. After importing the **mag** file and the **skylane130A tech** file, we extract the spice netlist. 

#### Extracting Spice netlist  
![Extracting Spice netlist](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/extracting%20spice%20netlist%20from%20MAGIC.png "")

#### Extracted Spice netlist 
![Extracting Spice netlist](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/spice%20netlist%20from%20MAGIC.png)

After extracting it, we invoke the NGSPice tool to performt the transiant analysis and find the 4 parameters mentioned at the beginning of this section.

#### Invoking NGSpice tool   
![Invoking NGSpice tool](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/invoking%20ngspice%20tool.png "")

If we observe in the above netlist, there are only components and its interconnects present. To perform the analysis, we need to include libraries for connecting the behavious of components to its libs. We need to add sources and mention the type of analysis.

First, we include the libs file for pmos and nmos. Observe the below lib file in which few parameters are mentioned. But we include the model from this lib. 

#### Parameters in lib file   
![Invoking NGSpice tool](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/invoking%20ngspice%20tool.png "")

In the same file, we have 32 models for pmos i.e., from pshort_model.0 to pshort_model.3l
#### pshort_model.0   
![pshort_model.0](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/pshort%20lib%20model%200.png "")

#### pshort_model.1   
![pshort_model.1](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/pshort%20model%201.png "")

#### pshort_model.2   
![pshort_model.2](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/pshort%20model%202.png "")

If you could observe among these models, width varies. When we compare remaining all models, we could observe changes in width as well as length. Hence, choosing appropriate model will decide our simulations.

After we complete including libs and specifying sources followed by type of analysis, we run simulation

#### Modified spice netlist
![modified spice netlist](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/modified%20spice%20file.png)
#### Transiant Analysis
![Transiant Analysis](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/new_trans%20analysis.png)

Now, we calculate the 4 parameters as part of library characterization
* Rise transition delay = Time taken for the output signal to reach from 20% of max value to 80% of max value.
* Fall transition delat = Time taken for the output signal to reach from 80% of max value to 20% of max value.
* Cell rise delay = Time difference between 50% of rising output and 50% of falling output
* Cell fall delay = Time difference between 50% of falling output and 50% of rising output

#### Rise transition delay
![Output 20% value](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/output_20_percntg.png)
![Output 80% value](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/output_80.png)

Value = 0.098ns

#### Fall transition delay
![Fall transition delay](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/fall_transition.png)

Value = 0.027ns

#### Cell rise delay
![Cell rise delay](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/cell_rise_delay.png)

Value = 0.028ns

#### Cell fall delay
![Cell fall delay](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/cell_fall_delay.png)

Value = 0.0041ns

Thus, we calculate the 4 parameters in the library characterization process.

### Understanding the DRC rules providing by PDK in the form of tech file.

We try to understand the DRC rules provided by Skylane130 PDK using its tech file and the MAGIC tool. First we extracted the drc_rules folder and then imported metal3 mag file into the MAGIC tool, to observe the DRC rules defined for it.

#### Metal 3 mag file in MAGIC tool
![Metal 3 mag file]()


#### DRC rules definition in Skylane130A tech file
![Tech file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-3/drc%20rules%20in%20skylane%20130%20tech%20file.png)

We can related these rules with the skylane google pdk website. Here under Design rules -> Periphery rules, all the DRC rules are defined for each metal, poly and so on.
Here is the [link](https://skywater-pdk.readthedocs.io/en/latest/rules/periphery.html#m3)

----

## Day-4 [Pre-layout timing analysis and importance of good clock tree]

### Developing LEF file for a standard cell design 
Before we move to this step, we need to make sure that the layout follows two main rules as per the requirements of the PnR tool. 
* The ports must stay on the interconnect of the grids
* The width and height of the std cell must be odd multiples of no.of grids

#### Grids in the layout
![Grids](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/grids%20in%20layout.png)

To understand this, we first need to convert the grids size in the MAGIC tool window to the track pitch size. For that, first we observe the values in the tracks.info file under the skylane130 files which is under Skylane130a PDK.

#### Tracks info file
![Tracks info](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/tracks%20info.png)

Here the first value defines the offset followed by the pitch width. We have these values for both X and Y axis. We transform the default grid size in the MAGIC tool to the tracks.info defined values. We use the command *grid x y xorigin yorigin* where x is horizontal pitch value, y is vertical pitch value, xorigin is horizontal offset value and yorigin is vertical offset value. After implementing this command, our grid structure looks like below.

#### Modified Grid structure 
![Modified grid structure](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/grids%20changed%20and%20pins%20on%20the%20interconnections.png)

By observing the above layout, it is clear that the two rules mentioned at the beginning of the section are met. Ports A and Y are on the interconnects of the grid and the size of the cell layout is 3 times of single grid. 

### Converting Std cell layout to LEF file.

Since the requirements are met, next step is to generate the LEF file. To do this, we go through two steps.
* Naming the pins
* Defining the pin attributes and its use

**Naming the Pin :**
To do this, we first select the area of the pin (suppose 'A') and click on *Edit* and *Text*. We now get the text dialogue box. We define the name, size of the text, order of the pin and the metal type. For Pins, we use *locali* as metal type and for power, we use *metal 3*.
#### Text Option in the Edit dropdown box (The reason to specify this was, I used delete and other edit commands from here itself previously to experiment)
![Text Option in the Edit dropdown box](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/text%20option.png)

#### Text Dialogue box for pin "A"
![Text Dialogue box for pin "A"](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/conveting%20pins%20to%20ports.png)

**Defining Pin use and attributes :**
In this step, we define the pin class as *input/output* and pin use as *signal/power/ground*. 
The commands we use to perform these are - 
 *port class [input/output]*
 *port use [signal/power/ground]*
 
 After defining the pins, we extract the LEF file from the layout by using command *lef write*. Below is the lef file generated for the inverter layout.
 #### LEF file for inverter
![LEF file for inverter](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/grids%20changed%20and%20pins%20on%20the%20interconnections.png)

Now since we generated the LEF file for the inverter, we need to include it with our design and test it. **The main goal for this whole process till now and next, is to learn how one can add a custom cell design to the OpenLANE flow and work with it.**

To proceed, we need to provide paths for the env variables of the OpenLANE using config.tcl file. There are three types of lib files -  fast, slow and typical.
Typical lib is the one used for the synthesis purposes and remaining two are same as the fast.lib and slow.lib files we usually come across in the ASIC flow.
Below are the snapshots of the three lib files

 #### Typical lib file
![Typical lib file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/typical%20lib%20file.png)
 #### Fast lib file
![Fast lib file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/fast%20lib%20file.png)
 #### Slow lib file
![Slow lob file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/slow%20lib%20file.png)

Now, we perform synthesis and check whether the custom cell got mapped by abc mapping or not. 

#### VsdInv getting mapped by the abc mapping tool
![VsdInv getting mapped by the abc mapping tool](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/custom%20cell%20got%20mapped%20in%20the%20abc%20mapping%20process.png)

During the synthesis optimization process, we have env variables which effect the timing and area.

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day4/variables%20effecting%20timing%20and%20area%20opt%20in%20synthesis.png)

Below is the timing after the synthesis.

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/timing%20after%20synthesis.png)

As we observed, the slack is too high and we need to reduce it. To do that, we use the previously mentioned env variables. 
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/setting%20the%20env%20variables%20for%20timing%20opt%20in%20synthesis.png)

After this, we need to merge our vsd_inv custom cell lef file and perform the synthesis, followed by floorplanning and placement. The reason we are proceeding to the placement despite having negative slack violations, is to check whether our custom cell got placed along with the picorv32a design or not.

![Merging LEF file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/merging%20custom%20cell%20lef%20to%20the%20design%20to%20the%20main%20lef.png)

If we could observe in the main lef file, we could see the custom cell lef details in it.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/after%20merging%20inv%20lef%20into%20merged%20lef.png)

![After opt synthesis](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/after%20optm%20synthesis.png)
Observe the reduced slack in the above picture after playing with the env variables at the synthesis stage.
After reducing the slack, we run floorplanning and placement in openLANE. We load the placement def file in the MAGIC tool.

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/def%20file%20after%20placement.png)

To find the presence of custom cell in the layout, I figured out three options - 
* Manually zooming and locating the cell
* Looking for the cell name in the cell manager lib in the MAGIC tool
* Using a simple command to locate the cell by giving the cell name as input

First one was tidious, so I preferred the second option. Below is the cell name in the cell manager dialogue box. The cell manager will show all cells present in the def file. Though this is not a correct alternative to the first opt, but it works. Below is the picture for it.

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/presence%20of%20inv%20cell%20in%20cell%20mng.png)

The third option is pretty interesting and easy as well. I will be updating about it sooner or later.

We succesfully integrated with the design. But, we need to reduce the slack (bring it out of negative value). To do this, we use the OpenSTA, the timing tool in the openLANE flow to opt the slack value. For that, we need to create the config file to use in the OpenSTA tool. Below shown is the config file - 
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/config%20file%20for%20sta.png)

After loading the config file in the tool, we observe the same slack values previously during the timing analysis after synthesis stage. If you observe the report, the fanout is high and also the load cap value is high. By reducing them, we can reduce the slack.
Below are the ss of the process I followed to reduce these two parameters in the **openLANE flow itself**.

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/high%20fanout%20.png)
#### setting fanout variable to 4 (from 6)
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/setting%20fanout%20var%20to%204.png)

#### Changing fanout and running synthesis.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/changing%20the%20fanout%20and%20running%20synthesis.png

Now, I will use the modified verilog file during the synthesis process and load that file in the STA tool.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/loading%20back%20.v%20file%20in%20the%20STA%20tool%20after%20fanout%20.png)

We now modified the fanouts. If you could observe the timing report, we have a buf chain in the design. This is because we enabled the BUFFERING env variable in the openLANE and performed the synthesis. Our next choice to improve the slack is to improve these buffers size. This will reduce the cap load and thus effects the slack. 

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/buffer%20chain%20effecting%20the%20slew.png)
![**Decreased slack after the modification**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/decreased%20slack%20by%20upgradind%20size%20of%20buff%20cell.png)

We repeat this until we get the improved slack value.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/upgrading%20buff%20size.png)
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/upgrading%20another%20buff%20size%20and%20decreasing%20slack.png)

After recursive modifications, we arrive to the below shown TNS and WNS values.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/final%20wns%20and%20tns%20values.png)

As we know, timing and area are inversly proportional. Though we reduced the slack, we got a huge hit on the area parameter. We can see this after we perform the placement stage with this netlist (First perform floorplan and do not perform synthesis cuz it will rewrite all modifications giving us back our original huge slack).

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/increase%20in%20area%20after%20running%20placement%20by%20using%20the%20modified%20netlist%20from%20sta.png)

### **4.CTS**

So, after getting the opt netlist in terms of timing, we proceed to next step i.e., building **Clock Tree Synthesis (CTS)**. We have few parameters for this stage as well, which effects the performance of this stage. 
![Env variables for CTS stage](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/cts%20satge%20env%20variables.png)

we run cts by giving command *run_cts*. Below is the snapshot where the CTS layers are created in the design.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/cts%20layers%20getting%20created%20while%20run_cts%20.png)
After performing CTS, we need to check for our slacks again. This is what we call as "Post-CTS timing analysis". Here we check the hold slack as well because it was not having any significance in previous stages due to absence of clocks. Before that, we need to make some preparations such as creating a db file in the *openROAD* application etc. 
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/postcts_timing_analysis_preps.png)
After preparations, we are good to go to report the timing. Below are the snapshots which shows the report and also shows that Setup and Hold slacks are met.
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/postcts_slack.png)

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/setup%20slack%20met.png)

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/hold%20slack%20met.png)

We all know that in CTS to adjust slew values, CTS engine (in ASIC Flow) uses special buffers called "Clock Buffers". Same happens in the OpenLANE flow. The CTS engine chooses between 4 types of clock buffers present in the list as shown below. Usally, CTS engine chooses the clock buffers from left to right and checks for slew values. We can force the CTS engine to choose required the clock buffer by removing other buffers from the list. This is want I did in the workshop. I modified the clock buffer env variable list and performed CTS again. Wtih this we get a more improved slack value. 

![**Clock buffer order**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/clk%20buffers%20order.png)
![**Improved Slack**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-4/clock%20buff%202%20improved%20slack.png)

----

## Day-5 [Final steps for RTL2GDS using tritonRoute and openSTA]

After performing CTS, we are now left with the last step in PnR flow i.e., Routing

### **5.Routing**

If you remember from the floorplanning stage, we didn't perform the PG routing (Power Ground routing). Unlike ASIC flow, PG Routing happens at the routing stage in the openLANE flow.
Hence we first build our Power Distribution Network by using *gen_pdn*. 

