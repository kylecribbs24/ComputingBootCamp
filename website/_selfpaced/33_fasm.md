---
layout: page
toc: true
title: FASM (FPGA Assembly)
slug: fasm
type: fpga_opensource
order: 4
---

## Overview

FASM (FPGA Assembly) is a textual representation of a bitstream. By assigning a symbolic name to each configurable thing in the FPGA, the resulting FASM file shows what features are specifically configured "on". These files provide an easy way to write programs that manipulate bitstreams. Modifying a textual FASM file is far easier than trying to modify a binary bitstream.

## Install

Follow the instructions for installing [Project X-Ray](https://byu-cpe.github.io/ComputingBootCamp/tutorials/xray/). 

## Lecture

On June 2, 2021, we had a lecture from Professor Nelson about [Project X-Ray](https://byu-cpe.github.io/ComputingBootCamp/tutorials/xray/) and FASM. The video is embeddded below.

<iframe width="800" height="600" allow="fullscreen" 
src="https://www.youtube.com/embed/6HGN8pQn_jA">
</iframe>

## Follow-Up Activities

***need to do this activity for myself to see if everything works properly***

* Generate a bitstream for the simplest design you can in Vivado (a 2-input AND gate).  Since prjxray cannot handle all FPGA devices, you will have to do it for a specific one: the `xc7a50tfgg484-1` part.
    * Also, if you don't have your top level ports mapped to physical pins then you will get Vivado errors.  Below is some code you can use in your .tcl script to make Vivado happy.  They set the IOB voltages and also tell Vivado to be happy with unconstrained pins.
```
set_property CFGBVS VCCO [current_design]
set_property CONFIG_VOLTAGE 3.3 [current_design]
set_property BITSTREAM.GENERAL.PERFRAMECRC YES [current_design]
set_property BITSTREAM.General.UnconstrainedPins {Allow} [current_design]
```
* Run $XRAY_BIT2FASM andgate.bit > andgate.fasm
* Examine the FASM file.  Can you find the LUT that implements the 2-input AND gate?  There are a number of reasons why the LUT INIT values may not be what you expect.  
    * The most common reason is the Vivado router will often rearrange the pin ordering on a LUT so it is easier to route signals to the LUT.  If it does this, that is equivalent to changing the order of the input columns in the truth table the LUT is implementing.  The result is that it also has to rearrange the ordering of the rows in the truth table to match.  Can you tell if it did that here?
    * The second most common reason is that a 6-input LUT in Artix7 is really two 5-input LUTs which share all their inputs.  They then have a MUX on their outputs which is controlled by the 6th input.  The tools may choose to use the 6-LUT for a function or it may choose to treat it like two 5-LUTs and use both of its outputs.  If you look at the diagram for a SLICEL in the Xilinx docs (search for "SLICEL diagram" on the web), you will see that the LUTs have **two** outputs.  Those are the outputs of the two 5-LUTs.  But, if the tools choose to treat it as a single big LUT then it uses the **O6** output and ignores the **O5** output.
* Repeat the above but for other kinds of gates.
* Do counters, shift registers, and memories and examine the FASM file to get a feel for how LUTs, flip flops, and BRAMs are represented.
    * NOTE: unless your memory is large enough, the tools will build the memory from flip flops or LUTRAMs instead of BRAMs.  If you want to see how to force it to use BRAM, pull down the Github "symbiflow/prjxray-bram-patch" project and look at the Verilog files in the "samples" directory - they each have a directive in them to force Vivado to use BRAMs, even if the memory they implement is small.

## Learn More

* FASM GitHub Repository - <https://github.com/SymbiFlow/fasm>
* FASM Documentation - <https://symbiflow.readthedocs.io/en/latest/fasm/docs/specification.html>
* FASM Output Support - <https://docs.verilogtorouting.org/en/latest/utils/fasm/>

