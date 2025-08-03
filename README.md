# Instructions for recreating the 431 keV plot in the 14N+n total cross section paper

This repository contains the data used for the 14N+n total cross section paper and this REAME provides instructions for recreating the 431 keV (lab frame) plot contrasting the Harvey and deBoer/ELBE data.

## Get the code and data

This analysis was run with the AZURE2 codebase.  First we will get the AZURE2 code.  If you have a working instance of AZURE2 built from the `api` branch you can skip this step.  The code needs to be from the `api` branch to take advantage of the variable-width convolution that has been implemented in this branch.

```
git clone git@github.com:rdeboer1/AZURE2.git
cd AZURE2
git checkout -b api
mkdir azure2
```

Note: This guide was run with commit `61028c55fca2385b856ce291619adc1c0aa86003`, so you may want to check out this exact commit if the current commit isn't behaving as expected.

With the last command we make a directory `azure2` because the scripts to run the Docker container connect mount this directory into the container.  Once we clone the repository that contains the data and the AZURE2 configuration files we will copy the relevant files to this directory.  This is also the directory that will contain the output from AZURE2.

```
cd ..
git clone

```

The expected directory structure is now

<pre>
-- AZURE2
   |-- azure2
   |-- etc.
-- 14N_n_totalCrossSection_0.1_to_12_MeV
</pre>

## Build the container

Now we will build the AZURE2 container with the command

```
cd AZURE2
source scripts/build.sh
```

## Copy over the needed files

Now we'll copy over the AZURE2 config files and the data files to the `AZURE2/azure2` directory so that when we run the script to run the container, the necessary files will inside the container.

```
cp stuff AZURE2/azure2
```

## Run the container

To run the container we will call the `run_gui.sh` script in the `AZURE2/scripts` directory.  This script will start the container and mount the `azure2` directory within the container.  It will also launch the AZURE2 gui.

```
cd AZURE2
source scripts/run_gui.sh
```

## Calculate the Elbe results and copy the output over to your plotting directory
Load the `11B+a_Elbe.azr` file and then `Save and Run`.  This will produce three `*.out` files in the `AZURE2/azure2/output` directory.

In the `11B+a_Elbe.azr` file, the convolution to account for spread in the beam energy is turned on.    

We need to rename these files so it's clear they're from the Elbe data and fit and copy them over to the working directory for the plotting.  I use this command:

```
$ cd AZURE2/azure2/output
$ find . -name 'AZUREOut_aa=*_R=*.out' -print0 | xargs -0 -I{} sh -c 'cp "{}" "../../../14N_n_totalCrossSection_0.1_to_12_MeV/output/Elbe_$(basename "{}")"'
```

## Calculate the Harvey results and copy the output over to your plotting directory
Load the `11B+a_Harvey.azr` file and then `Save and Run`.  This will produce three `*.out` files in the `AZURE2/azure2/output` directory.  

In the `11B+a_Harvey.azr` configuration file, there is no convolution to account for the energy spread of the beam.  The authors of the Harvey paper argue that the beam spread from their experiment has a negligible impact at this energy.

We need to rename these files so it's clear they're from the Harvey data and fit and copy them over to the working directory for the plotting.  I use this command:

```
$ cd AZURE2/azure2/output
$ find . -name 'AZUREOut_aa=*_R=*.out' -print0 | xargs -0 -I{} sh -c 'cp "{}" "../../../14N_n_totalCrossSection_0.1_to_12_MeV/output/Harvey_$(basename "{}")"'
```

## Plot the Harvey result and the deBoer/Elbe data on the same graph


