# ANTICO
AxCalc.py is a python script to generate a whole set of inputs to Virtual AFM. Ultimately, it will be run automatically by the browser, but it needs to be thoroughly tested for now.

Before run...
--------------------------
The script is written in the Python 3 standard (generally applicable). However, it requires the installation of several additional Python packages not included in the standard installation. These are NumPy, SciPy, and MDAnalysis.
Install them using the pip command:

 _pip install numpy scipy MDAnalysis_

or using conda:

_conda install numpy scipy MDAnalysis_

Runing
------------------------
Run the program in terminal (Linux) by typing in the command line (being in the directory where you downloaded the script):

_python AxCalc.py_  (or python3 AxCalc.py  if python3 is not the default python installation) in the same line we added parameters.

We call the program by giving it the following parameters:

_python AxCalc.py file.pdb file.psf file.vel file.coor file.xsc toppar.zip template.inp template.run ‘selection constraints’ ‘selection pull’_

file.pdb        - the PDB structure of our system

file.psf        - the PSF file for our system

file.vel        - velocity information- a restart file from NAMD equilibration simulation   

file.coor    - current coordinates of all atoms in the system (also from the NAMDs equilibration)

file.xsc        - contains the periodic cell parameters and extended system variables

toppar.zip    - if you have only one file with Charmm parameters, write it here
  (e.g. param1.inp). However, if there are more parameter files, you have to 
  put them into the toppar folder and "zip" it (_zip -r toppar.zip toppar_)

template.inp    - Here we have an input file to namd, in which we set all simulation parameters we need. Based on this file, the input to SMD will be generated, so it is important that the section describing SMD and constraints (SMD on ... constraints yes, etc.) is present. The exemplary input file can be found in the TEST.zip folder. To avoid possible artifacts, please make sure that SMD simulations are performed in the NVT ensemble (the pressure control should be set off).

template.run    - A sample script to run the simulation on the computer you intend to count - containing the namd running line. Input and output files will be defined as _INPF_ and _OUTF), so this is how they should be treated in the namd running line (_/home/user/NAMD/namd2 +p2 $INPF > $OUTF 2>&_). The exemplary template.run file can be found in TEST.zip.

‘selection’    - selections of constrained and pulled atoms 
 in SMD simulation. These are text variables, necessarily in quotation marks ''. The convention for atom selection is as in MDAnalysis (https://docs.mdanalysis.org/1.1.0/documentation_pages/selections.html) i.e., 'name CA and protein and segid A B C' or 'name CA and resid 1:55 66:128', or 'name CA and resname PRO ALA NBD'. Unfortunately, there is no 'chain' selection, so you have to use 'segid' instead.
It is recommended to restrain only CA atoms. The script produces an input file to SMD (SMD_constraints.pdb) containing the corresponding values in columns O and B.

The output
--------------------------------
The program will generate an Output directory containing the input files and subdirectories corresponding to the "pull" directions. Each subdirectory contains an appropriately prepared input file to NAMD, and a bash script to run the given simulation (based on the provided template.run). You can run these scripts each separately:

 _. Output/SMD_theta_0_phi_0/run.bash_ (the "dot" will run the script exactly where the run.bash file is)
 
or together using the master.run script:

_./master.run_

These will start the SMD simulation.

For visualisation purposes, the program generates a tcl script for VMD (_vmd_script.tcl_), which draw the bunch of vectors 
representing the directions of pulling. 

Analysing the output
---------------------------------
The Analysis.py script facilitates analyzing SMD simulation results. It iterates through subdirectories in the Output directory and extracts SMD information from mdrun.log files. Additionally, it checks the corresponding dcd files and calculates the number of hydrogen bonds formed between a given protein selection for each simulation.
The program can be called using the following parameters:
_python Analysis.py Output 'selection 1' 'selection 2'_
where Output is the directory with SMD data, selection 1 is the constrained part of our system, and selection 2 is the pulled part of our system. 


