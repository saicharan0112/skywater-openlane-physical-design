# ASIC Back-end flow vs OpenLANE flow

Apart from performing the whole openlane flow using PICORV32a code, I got an opportunity to compare and find the differences between ASIC flow and the OpenLANE flow. Instead of
comparing individual steps, I will be comparing individual flows since there is a diff in the steps of building the chip. This gives us a clear idea on the pros and cons w.r.t both flows.

**ASIC Flow**

<center><img src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2F1.bp.blogspot.com%2F-G8N6QZlK_SE%2FXg-BaJ77y_I%2FAAAAAAAAiKw%2FHGPSFpEApGE0Cfiak0OO_95O_cdCa59egCLcBGAsYHQ%2Fs1600%2F22.PNG&f=1&nofb=1"></center>

**OpenLANE Flow**
<center><img src="https://github.com/efabless/openlane/raw/master/docs/_static/openlane.flow.1.png"></center>

## Table of Contents

* [**ASIC flow**]()
* [**OpenLANE flow**]()

### ASIC (Application Specific Intergated Circuit) Back-end flow

ASIC flow is the RTL2GDSII process where we create a chip starting from the logic to the GDSII file which we send it to the foundries like TSMC, Global Foundaries, Samsung Semiconductors and Intel(recently established foundry business). 
Front-end flow completely deals with the logical part of the design by verifying whether the logic is meeting the requirements and specifications or not. We run a recursive verfication steps to make sure that the logic is working under all available inputs. Once we are confident about the logic written, we move furthur to do the physical implementation of it.

Back-end flow completely deals with the optimization of the design, converting it to a physically implementable stage and then physical implementation itself followed by verification and fabrication. 

#### 1. Synthesis 
In synthesis, we map the logical gates to the library gates and optimizing it accordingly. 

#### 2. Timing Analysis
Once we are ready with the optimized netlist, we need to see that our chip's logic is meeting the timing or not (Setup and Hold violations). We try to optimize it according to the SDC constraints, by adding cells and changing the strategy during re-synthesis. 

#### 3. Floorplanning
Once we are good with the netlist generated, we move to the actual physical design implementation of the cell.

  > **Defining Aspect ratios, Core utilization ratios**
    - We define the boundary dimensions our chip should follow through out the designing process. This is cruial since it effects the congestion.

  > **Power Planning**
    - We insert power rings, strips and the special routing which supplies the power and ground to standard cells.
      
  > **IO Placement**
    - We, then, place the IO pads which act as the communication channels between our chip and the outside world.
      
  > **Tap cells placement**
    - Physical cells placement will help in avoiding future issues like latch-up etc.
      
  > **Macro Placement**
    - We place the macros by following few rules.

#### 4. Placement
Here we place our netlist on the floorplan. Placement happens in three steps - Global Placement, Legalizing locations, Optimizing placement. Blockages are placed in required locations to avoid congestion (avoiding routing and placing of standard cells), before the placement stage starts.

#### 5. CTS
In this stage, we build the clock network in our chip. We follow a CTS algorithm (mostly the H-Algorithm) and build the clock network.
After this, we perform timing checks and optimizations. We try to clear all setup and hold violations in this stage. Usually, these violations will be reduced after routing since we have the actual wires.

#### 6. Routing
In this stage, we will perform the routing. Similar to placement, routing is divided into two steps - Global routing and Detailed routing. In global routing, GCells are created which create a rough routing for our design. In the detailed routing, the actual routing happens. 
After performing routing, we need to check for DRC violations and Connectivity violations. We need to check for timing as well.

#### 7. SPEF Extraction
Once we get our routed design, we extract SPEF files which is basically wire info. This will be used in optimizing the design while performing recursive steps.

#### 8. Physical Verification
We now perform the verification of our design using rule decks. We check for various violations like DRC, LVS, ERC, Antenna violations etc. If you wonder about the need to check for these violations even after clearing DRCs after the routing stage, you are correct. We clear all physical issues in this stage and nothing should be missed from this stage. 

#### 9. GDSII
Once we are sure about the clean design, we extract the GDSII file which is the file format used by the foundry to manufacture the chip

**_Few things which are to be noted are_** :
1. The complete process is iterative
2. We check for timing at almost every stage
3. Many things are hidden from each other since PDKs, LEFs, LIBS that are supplied from the foundry and EDA tools are proprietary (thus their algorithms involved in the process as well). 
4. Since everything is proprietary, not everyone are allowed to build a chip (the final goal) 

---

### OpenLANE flow

OpenLANE flow is an automated RTL2GDSII flow where all required tools are embedded into it and you have complete control of each process. We control them by using env variables which will be discussed at each stage since they are unique for each of them. 
 
 **If you like to get a picture of the flow for a design, please refer the workshop journey from Synthesis to GDSII where PICORV32a design is considered. Below are the links for the steps in the flow**
 
> [**1. Synthesis**](https://github.com/lankasaicharan/skywater-openlane-physical-design#1synthesis)

> [**2. Floorplanning**](https://github.com/lankasaicharan/skywater-openlane-physical-design#2floorplanning)
 
> [**3. Placement**](https://github.com/lankasaicharan/skywater-openlane-physical-design#3placement)

> [**4. CTS**](https://github.com/lankasaicharan/skywater-openlane-physical-design#4cts)

> [**5. Routing**](https://github.com/lankasaicharan/skywater-openlane-physical-design#5routing)
  
> [**6. RC Extraction**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#6rc-extraction)
  
> [**7. Physical Verification**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#7physical-verification)
   
> [**8. GDSII**](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/README.md#8gdsii)


This OpenLANE flow is specially designed for **_no human interaction based RTL2GDSII flow_**. Hence we have automated mode and interactive mode to run the OpenLANE flow. Before we start the flow, we need to config the OpenLANE flow by mentioning the essential/design specific parameters in the config file. In the config file, we have the following environment variables which are available for the flow to start. 

`DESIGN_NAME VERILOG_FILES CLOCK_PERIOD CLOCK_NET CLOCK_PORT`

#### Snapshot of the basic configs file
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/required%20env%20variables%20to%20start%20the%20flow.png)
Once these are setup, we will envoke the flow using a tcl script (in interactive mode) and start the flow by preparing the flow according to the design. 

`package require openlane 0.9`

`prep -design <design_name> [-tag <runs_file_name>] [-overwrite]`

This will prepare the design the design by considering lefs, config files mentioned previously etc. After this step, we are ready to start our flow

### 1. Synthesis ( Tools: Yosys, abc, OpenSTA )
We just use command `run_synthesis` to perform synthesis which created a netlist file. It also performs the post-synthesis STA and generates reports at the end. But there are few parameters which can be set to customize our synthesis procedure. Below are few parameters - 

`SYNTH_BUFFERING SYNTH_SIZING SYNTH_STRATEGY` and many more. 

#### Snapshot of the parameters for the Synthesis stage
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/synthesis%20variables.png)

### 2. Floorplanning ( Tools: init_fp, ioplacer, tapcell )
We just use command `run_floorplan` to perform floorplanning in OpenLANE. In this stage, core and die areas are decided, I/O pins are placed and physical cells are placed.

#### Snapshot of the parameters for the Floorplan stage
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/floorplanning%20env%20variables.png)

### 3. Placement ( Tools: RePLace, Resizer, OpenPhySyn, OpenDP )
We just use command `run_placement` to perform Placement in OpenLANE. In this stage, placement is done in three steps - Global placement, Detailed placement, optimization.

#### Snapshot of the parameters for the Placement stage
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/placement%20env%20vars.png)

### 4. CTS ( Tools: TritonCTS )
We just use command `run_cts` to perform CTS in OpenLANE. In this stage, clock tree is built. We do not have many parameters to control the CTS, but have few important ones as shown below.

#### Snapshot of the parameters for the CTS stage
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/cts%20env%20vars.png)

### 5. Routing ( Tools: pdn, FastRoute, TritonRoute )
We generate the Power Distribution network for our design using command `gen_pdn`. We then use command `run_routing` to perform routing in OpenLANE. In this stage, same as ASIC flow, routing is done is two parts - global routing and detailed routing

#### Snapshot of the parameters for the Routing stage
![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/routing%20env%20vars.png)

### 6. RC Extraction (Tools: SPEF Extractor)
We can extract spef files using SPEF Extractor tool. 

### 7. Physical Verification (Tools: MAGIC, NETGEN)
MAGIC tool is used for DRC and antenna checks whereas NETGEN is used for the LVS checks

### 8. GDSII (Tools: MAGIC)
In the final stage, we create the GDSII file from our **_DRC CLEAN DESIGN_** using MAGIC tool. This will terminate the RTL2GDSII flow. 

--- 

**Below is the OpenLANE flow including commands and its function**

<img src="https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/Openlane%20flow%20(1).png" alt="OpenLANE flow" height="700" width="400"/>

---

**This is the table showcasing available tools for each stage in chip designing process.**

![](https://github.com/lankasaicharan/skywater-openlane-physical-design/blob/main/asic%20vs%20openlane/tools%20comparision.png)


## Acknowledgments
1. Kunal Ghosh - (Co-founder, VLSI System Design) - One who helped me to refine the above content
1. ASIC Flow image - physicaldesign4u.com
2. OpenLANE Flow image - official github repo of OpenLANE


