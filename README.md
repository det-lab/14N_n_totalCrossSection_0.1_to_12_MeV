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

## Predict the theoretical values for the best-fit to the Harvey data

## Predict the theoretical values for the best-fit to the deBoer/Elbe data

## Plot the Harvey result and the deBoer/Elbe data on the same graph

## Other investigations

You can explore the difference the convolution makes.  Originally we explored the convolution as something that could reconcile the Harvey data (where the resonance is best fit with 7/2 spin) and the Elbe data (where the resonance is best fit with 5/2 spin).  By turning the convolution on and off, you can see that the impact of the beam resolution on this resonance peak (which is minimal).

