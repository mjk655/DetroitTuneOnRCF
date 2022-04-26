Outlined here are instructions created by Raghav to run pythia8 and produce HepMC2 data that can be used with RIVET, and to run RIVET with some output from pythia. There are two dedicated setup scipts, one for pythia and one for rivet. A fresh terminal will be needed to run each, and do not load any STAR library prior to these steps.

# Running PYTHIA8

In a fresh terminal immediately start a bash session and source the pythia setup script from the project root directory

> bash

> source setuppythia83.sh


Create a directory from which we will run pythia, e.g., "/example" will be used here. Enter directory and copy all the materials from Raghav's directory

> mkdir example

> cd example

> cp /gpfs01/star/pwg/elayavalli/pythia8303/examples/* .

We will be using main42_hepmc2.cc to generate the pythia events, and for now PYTHIA8_10pthat80_20MEvents_D_200GeV.cmnd for input. Compile with 

>/cvmfs/sft.cern.ch/lcg/releases/gcc/8.2.0-3fa06/x86_64-centos7/bin/g++ main42_hepmc2.cc -o main42_hepmc2 -I/gpfs01/star/pwg/elayavalli/HepMC-2.06.11-build/include -I/gpfs01/star/pwg/elayavalli/pythia8303/include -O2 -std=c++11 -pedantic -W -Wall -Wshadow -fPIC  -L/gpfs01/star/pwg/elayavalli/pythia8303/lib -Wl,-rpath,/gpfs01/star/pwg/elayavalli/pythia8303/lib -lpythia8 -ldl -L/gpfs01/star/pwg/elayavalli/HepMC-2.06.11-build/lib -Wl,-rpath,/gpfs01/star/pwg/elayavalli/HepMC-2.06.11-build/lib -lHepMC

Run pythia now with

> ./main42_hepmc2 PYTHIA8_10pthat80_20MEvents_D_200GeV.cmnd PYTHIA8_10pthat80_20MEvents_D_200GeV.hepmc


If everything worked well there should now be a PYTHIA8_10pthat80_20MEvents_D_200GeV.hepmc file in your working directory. This is now the end of the pythia part.

# Running RIVET
Open a fresh terminal and source the rivet setup file:

> bash

> source setuprivet313.sh

Here, we will work Raghav's example RIVET anaysis to plot identified hadron distributions. So lets copy that file to our pythia directory (or anywhere)
> cd example

> cp /gpfs01/star/pwg/elayavalli/RIVET_ANALYSIS/TEST_MYANA.* .

Compile the analysis with
> rivet-build TEST_MYANA.cc

This will create a RivetTEST_MYANA.so file. Now we will run rivet with this analysis using the HepMC data from the last step
> rivet -a TEST_MYANA --pwd PYTHIA8_10pthat80_20MEvents_D_200GeV.hepmc -H output.yoda

Note "-a" here tells rivet the name of the analysis and --pwd tells rivet to look in this directory. 
Now we can plot the MC using
> rivet-mkhtml --mc-errs output.yoda:'pythia8.3' 

This will put all the plots created in TEST_MYANA in rivet-plots/TEST_MYANA. Copy to local machine and try opening index.html.

