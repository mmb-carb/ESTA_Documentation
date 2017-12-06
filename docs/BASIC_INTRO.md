# ESTA Basic Introduction

> Emissions Spatial and Temporal Allocator

This document explains what ESTA is, and what it does. This is not meant as a guide to using ESTA; you will find that in the [User Guide](USERS_GUIDE.md).


## Introduction

The ESTA (Emissions Spatial and Temporal Allocator) model applies spatial and temporal surrogates to emissions to create top-down emission inventory files. The model is very general and can apply spatial and/or temporal surrogates to any kind of emissions file. However, ESTA was originally developed with the goal of gridding on-road emissions as a replacement for the DTIM model. Currently, the only example cases released with the ESTA model are for running on-road inventories.


## How It Works

ESTA is written in Python 2.7, using only free, open-source technology. It is a command-line program, executable on Linux, PC, or Mac. ESTA has a modular design to make it easier to upgrade, improve, and modify. The flow of the program is easy to understand, there are six basic steps:

![Figure 1: ESTA workflow, generic version](resources/esta_box_diagram_1.png)
*Figure 1: ESTA workflow, generic version*

As shown in Figure 1, the ESTA workflow is straight forward. In the first step, emissions files are read into memory. In the second step, spatial distribution files are read and spatial surrogates are created. In the third step, temporal distribution files are read and the temporal surrogates are created. In the fourth step, the spatial and temporal surrogates are applied to the emissions and a gridded inventory is created in memory. In the fifth step, custom-formatted output files are created from the gridded inventory. There is an optional sixth step, where quality assurance tests are run on the output files.

The above workflow is very general and can apply to gridding an on-road inventory, or any other kind of inventory. However, in practice, your workflow might be more complex. For instance, what if you want to read in multiple different kinds of emissions files? What if you want to read separate files for different kinds of temporal profiles: diurnal vs monthly? ESTA's modular design helps support these common types of workflows by allowing each box in Figure 1 to be replaced by a list of boxes / steps.


## Example - On-Road with EMFAC2014

To better understand ESTA, let us look at an example.

Figure 2 shows the workflow for spatially gridding the on-road emissions from EMFAC2014. Notice some of the steps are repeated for different file types.

![Figure 2: ESTA workflow, EMFAC example](resources/esta_box_diagram_on_road.png)
*Figure 2: ESTA workflow, EMFAC example*

As shown in Figure 2, there are two implementations of the first "read emissions" step. This is because ARB reads monthly EMFAC files for Heavy-Duty Diesel emissions, but daily files for all other vehicle types. Being able to support reading two different kinds of input emissions files is important. The third step is also different, because ARB uses both diurnal and day-of-week temporal profiles from the [California Vehicle Activity Database](https://www.arb.ca.gov/research/apr/past/11-316.pdf) (CalVAD). Steps 2, 4, 5, and 6 are still just single boxes, but they have been replaced with the specific logic ARB needs when processing on-road emissions. For instance, step 5 writes NetCDF output files, formatted for CMAQ modeling. In practice, step 6 might have quite a few more boxes, as we can imagine wanting to do a lot of testing on our final output files.

ESTA is designed so that developers can create new versions of the steps above to fit their own unique problem. This modularity allows developers to create new output files, read new input files, and change their workflow. The goal of ESTA is to link the workflow together with minimal fuss.


[Back to Main Readme](../README.md)
