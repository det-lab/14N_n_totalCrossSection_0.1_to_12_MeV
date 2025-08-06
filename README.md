# Instructions for recreating the 431 keV plot in the 14N+n total cross section paper

This repository contains the data used for the 14N+n total cross section paper and this README provides instructions for recreating the 431 keV (lab frame) plot contrasting the Harvey and deBoer/ELBE data.  While this is not a replication of the full analysis, it does have the final parameters for the AZURE2 `.azr` files and all of the datasets used, and illustrates what the units and frames of all the input and output files are.

## Get the code and data

### Get AZURE2
This analysis was run with the AZURE2 codebase.  First we will get the AZURE2 code.  The code needs to be from the `api` branch to take advantage of the variable-width convolution that has been implemented in this branch.  You will need a github account to clone the code.

```
git clone --branch api git@github.com:rdeboer1/AZURE2.git
```

Note: This guide was run with commit `61028c55fca2385b856ce291619adc1c0aa86003`, so you may want to check out this exact commit if the current commit isn't behaving as expected.


### Get the data and configuration files
Now we will get the repository that contains the data and the configuration files for the 14N+n total cross section analysis.

```
cd ..
git clone git@github.com:det-lab/14N_n_totalCrossSection_0.1_to_12_MeV.git
```

### Make a working directory and check your directory structure
Now we'll make a working directory inside `AZURE2`.  This directory will eventually contain the `.azr` files, data files, and output files for `AZURE2`.

```
cd AZURE2
mkdir azure2
```

The expected directory structure is now

<pre>
-- AZURE2
   |-- azure2
   |-- etc.
-- 14N_n_totalCrossSection_0.1_to_12_MeV
</pre>

## Copy over the needed files

Now we'll copy over the AZURE2 config files and the data files to the `AZURE2/azure2` directory so that when we run the script to run the container, the necessary files will be inside the container.  AZURE2 also expects an `output` and `checks` directory by default, so we make those as well.

```
cd azure2
mkdir data
mkdir output
mkdir checks
cp ../../14N_n_totalCrossSection_0.1_to_12_MeV/config_files/* .
cp ../../14N_n_totalCrossSection_0.1_to_12_MeV/data/* data/
```

## Build the container

Now we will build the AZURE2 container with the command

```
cd AZURE2
source scripts/build.sh
```

> [!IMPORTANT]
> If the file `scripts/run_gui.sh` does not exist in your `AZURE2` repository, you are working on the wrong branch.  For these instructions to work, you need to switch to the `api` branch.  To do so, try
> ```
> git checkout -b api
> git pull
> ```
> You can check that you are on the correct branch with the command `git branch`.

## Run the container

To run the container we will call the `run_gui.sh` script in the `AZURE2/scripts` directory.  This script will start the container and mount the `azure2` directory within the container.  It will also launch the AZURE2 gui.

```
cd .. # move to the top-level directory of the AZURE2 repository
source scripts/run_gui.sh
```

## Calculate the Elbe results and copy the output over to your plotting directory
Load the `11B+a_Elbe.azr` file and then `Save and Run`.  This will produce three `*.out` files in the `AZURE2/azure2/output` directory.

In the `11B+a_Elbe.azr` file, the convolution to account for spread in the beam energy is turned on.    

We need to rename these files so it's clear they're from the Elbe data and fit and copy them over to the working directory for the plotting.  I open a new terminal (as the current terminal is still running the AZURE2 GUI) and use this command:

```
cd AZURE2/azure2/output
find . -name 'AZUREOut_aa=*_R=*.out' -print0 | xargs -0 -I{} sh -c 'cp "{}" "../../../14N_n_totalCrossSection_0.1_to_12_MeV/output/Elbe_$(basename "{}")"'
```

## Calculate the Harvey results and copy the output over to your plotting directory
Load the `11B+a_Harvey.azr` file and then `Save and Run`.  This will produce three `*.out` files in the `AZURE2/azure2/output` directory.  

In the `11B+a_Harvey.azr` configuration file, there is no convolution to account for the energy spread of the beam.  The authors of the Harvey paper argue that the beam spread from their experiment has a negligible impact at this energy.

We need to rename these files so it's clear they're from the Harvey data and fit and copy them over to the working directory for the plotting.  I use the terminal previously used to copy the output files and use this command:

```
find . -name 'AZUREOut_aa=*_R=*.out' -print0 | xargs -0 -I{} sh -c 'cp "{}" "../../../14N_n_totalCrossSection_0.1_to_12_MeV/output/Harvey_$(basename "{}")"'
```

## Plot the Harvey result and the deBoer/Elbe data on the same graph

Now you're ready to run the notebook `14N_n_totalCrossSection_0.1_to_12_MeV/fig_13.ipynb`.  

