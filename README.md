# Skywater-openlane-physical-design

VSD SKy130 Openlane Physical Design is a 5-day workshop where I learnt about the opensource EDA tools involved in the synthesis, physical design and gdsii stages. 
First, I will be briefing about the Openlane flow and the tools involved in it. Later, I will documenting my journey of 5 days in the workshop.

## OpenLane 

OpenLane Flow is an automated RTL2GDSII flow which involves various opensource tools like OpenSTA, Yosys, Netgen, Magic etc. Below is the image illustration the flow along with
the tools involved. The main motto of this Openlane is to automate the RTL2toGDSII flow and producing clean GDSII files. "_OpenLANE is a tape-out-hardened flow that addresses two main use cases: hardening a macro and integrating
a System-on-a-Chip (SoC)._" - the research paper introducing OpenLANE (click [here](https://woset-workshop.github.io/PDFs/2020/a21.pdf) to view)

![OpenLane Flow - from official github repo](https://github.com/efabless/openlane/raw/master/docs/_static/openlane.flow.1.png "OpenLane Flow - from official github repo")

## Table of Contents

* [**Day-1 [Inception of open-source EDA, OpenLANE and Sky130 PDK]**](https://github.com/Lanka1919/skywater-openlane-physical-design#day-1-inception-of-open-source-eda-openlane-and-sky130-pdk)
* [**Day-2 [Good floorplan vs bad floorplan and introduction to library cells]**](https://github.com/Lanka1919/skywater-openlane-physical-design#day-2-good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)

-----------------

## Day-1 [Inception of open-source EDA, OpenLANE and Sky130 PDK]

The understanding of a chip started from the package and went till the macros inside a chip. Later, basic understanding of RISC-V ISA was given followed by a detailed understanding on how softwares interact with hardware and what are the intermediate components that are involved in the process. 

Moving forward, I was introduced to the OpenLane ASIC flow and the tools involved in it. In a nutshell, we get to give the RTL file (usually a .v file) and the PDK, in this case it is the skywater130 PDK, to this flow and the GDSII (clean) will be generated. OpenRoad application is the one where the PnR happens. Though for detailed routing, we need to move out of the OpenRoad application for using the TritonRoute (another tool) which is part of the flow. 

#### Invoking OpenLane and performing initial preparations

![Invoking OpenLane and performing initial preparations](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/invoking%20opelane_initial_settings.png "Invoking OpenLane and performing initial preparations")

#### Running Synthesis

![Running Synthesis](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/running_synthesis.png "Running Synthesis")

#### Summary of cells in the design after optimization in the synthesis

![Optimized netlist summary](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/cells_summary_after_synthesis_and_optimization.png "Optimized netlist summary")

#### Slack Report of min and max paths of the clock

![min_path](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/slack_report_of_min_path.png "Min Path Slack Report")

![max_path](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-1/slack_report_of_min_path.png "Max Path Slack Report")

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Day-2 [Good floorplan vs bad floorplan and introduction to library cells]

A brief understanding on floorplan stage and the factors involved in it like core utilization, aspect ratio etc., was given. Later, I came across the method of loading the prev run file while preparing the openlane. While performing floorplan, I came across the precedency of various config files. Those files and the order of precedence is shown below.
### **1.Floorplanning**
#### Default floorplan config file
![default floorplan config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/floorplan_defaults_config_tcl_file.png "Default file")

#### config file
![config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/config_tcl_file.png "config file")

#### Sky130a config file
![Sky130a config file](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/skywater_openlane_config_tcl_file.png "Sky130a config file")

#### Precendency
![Precendency](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/Day-2/Screenshot_2021-04-08%20Sai_Charan_Lanka%20VSD%20-%20Intelligent%20Assessment%20Technology%20%5BVSD%20-%20IAT%5D%20powered%20by%20jnaapti.png "Precendency")

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

### **2.Placement**

After performing floorplan, we place the standard cells (which were already present at the bottom left of the design) and observe the DRCs. We do this by giving the command *run_placement* in OpenLANE and invoke the **Magic** tool to view the layout of the design after placement of std cells.

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
