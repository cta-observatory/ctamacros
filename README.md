ctamacros
==============

Project description
--------------

ROOT-based macros used by CTA PHYS group to compute sensitivity, developed by Daniel Mazin and Emma de Oña Wilhelmi
 

Dependencies
--------------

The only dependency required to run these tools is ROOT.

If you would like to use the python implementation, you will need python with numpy and possibly the ebltable module.


Installation
--------------

No installation is required to run the macros. Just clone the git repository:

```shell
git clone https://github.com/cta-observatory/ctamacros.git
```

As soon as you have the code, you can start executing examples:

```shell
cd ctamacros
root testCTA_v6simple.C
```

To use the python implementation, add the ctamacro path to your PYTHONHOME environment variable. 
For instance in bash, type

```shell
export PYTHONPATH=".:path/to/ctamacros:$PYTHONPATH"
```

Documentation
--------------

As a new project, it still lacks documentation for all functionalities. Bellow available documentation on skymap simulation.


SkyMap Simulation Program
-------------------------



How you simulate an image:

1) Edit the main script makeCTAmaps_v1.C and modify "USER SIMULATION OPTIONS"

- If you want to create your own morpholgy select:

```c++
create_excess=true;                                      // This option allows you to create a morphology shape with a given spectrum (see below for options)
whichshape=[0 or 1 or 2 or 3];                           // Shape to create 0: Gauss, 1: Shell-type, 2: Composite, 3: Cooling
```

Select a position in the camera (taking into account that theta2 = (x2 + y2) < 4.5^2 )

```c++
user_Xpos=0;                            // For the ex. in the 2D histogram   -258.7;
user_Ypos=0;                            // For the ex. in the 2D histogram -40.
```

Select how you want to simulate your signal

```c++ 
// In case you select whichshape=[1 or 2]
ExternalGaus=0.2;                       // define the inner and outer radius of the shell (substracting two gaussians functions)
InternalGaus=0.18;
```

Select which type of spectral shape you want to simulate

```c++
whichspectrum=[0 or 1];                           // 0: PL, 1: PL+Exp
Gamma=2.7;                                        // photon spectrum 
Ecut=1.;                                          // Exponential Cutoff if selected (whichspectrum=1)
FluxRatio=2;                                      // Flux Ratio between the outer and inner emission region (for cases 2 and 3) Fshell/Fpoint_like or Fextended/Fpoint_like
```

If you want to read the morphology from an external file (i.e. j1713_sim.root):

```c++
create_excess=false;
fshapename = "./j1713_sim.root";       // Name of your root file
fhistoname = "Excess";                 // Name of your TH2D histogram
```

- To create your photon maps

```c++
printonlyex=0;                                   // Include background fluctuations [0] or not [1]
Is_IFAEoffsetEA=true;                            // Include IFAE-Extended CTA response -> If it is set to false it will read the on-axis configurations
configpath = "./config/";                        // Directory with your configurations. 
differential=true;                               // Differential or Integral Energy Bins
Ebins=1;                                    // number of energy bins -- the energy bins are defined to be constant in logaritmic scale
Eth=0.05;                                      // Energy threshold
Emax=100;                                      // Maximum energy
observationTime=50*60*60;                       // observation time in seconds
rangex=3;                                      // 3 degree in X axis
rangey=3;                                      // 3 degree in Y axis
gaus_smooth=0;				       	 // Smoothed your excess map with a gaussian with sigma : smooth
smooth=0.06;
```

- Once you are derived your photon map you can:

Fit your Source

```c++
fitsrc=true;
```

Obtain radial profile 

```c++
radialprofile=true; 
user_xslice=true;                           // Select X or Y axis for your profile
user_halfwidth=10.;                       // Size of your profile
user_range1=0;                            // if range1 and 2 are set to 0, the whole map is used
user_range2=0;
user_average=false;                         // Average bin content
```

- Finally you can save your results

Save your skymaps ("\Basename\_PlotsResults.root")  (\Basename\ is given as an input in the command line when executing the script)

```c++
saveplots=true;                             // Save Plots
```

Save the results of the fit ("\Basename\_FitResults.dat")

```c++
savefit=false;                              // Save Fit Results in a text file 
```

and you can display the skymaps on the screen

```c++
plotresults=true;                           // Plot results
```

or you can obtain the significance map: 

```c++
Calc_Significance=true
```

2) To run the simulation:

```c++
root
.L makeCTAmaps_v0.C+
makeCTAmaps("Config - [E or B]", % Crab, Gaussian_Sigma, "Basename")
```

i.e. ```makeCTAmaps("E",1,0.1,"MySimulation")```

3) To convert from a FITS file to root 2D histogram

For the moment this option is only in a beta version. But you can use the script we provide called convertFITSto2D.C with astroroot:
convertFITSto2D(TString file_name, TString image_name,TString outfile_name)

Ex.:

```c++
root
.L convertFITSto2D()
convertFITSto2D("hess_rxj1713_2005.fits","cor_excess_smooth_zoom","j1713_sim.root")
```

The file hess_rxj1713_2005.fits can be also found in:

http://www.mpi-hd.mpg.de/hfm/HESS/pages/publications/auxiliary/auxinfo_rxj1713_paper3.html


Remember to change the file name in your makeCTAmaps option and switch to 
```c++
create_excess=false;
fshapename = "./j1713_sim.root";       // Name of your root file
fhistoname = "Excess";
```

Using ctamacros in python
-------------------------
See the detailed ipython/jupyter notebook example_ctamacro_for_python.ipynb for a simple usage example.

License
-------

MIT: see [License.md](License.md)
