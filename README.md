# genproductions
This forked repo is primarily for calculating cross section from MiniAODsim, which has been kindly suggested by Jan-Frederik Schulte. He has updated Utilites/compute_cross_section.py code to work with python3, which I have subsequently pushed to this repo.

# XS calculation instructions

NOTE: Due to weird XrootD shenanegans, as of Dec 11 2024, this setup doesn't work on hammer nor purdue analsysi facility. It does however work on lxplus.
Instructions are edited from the original twiki instrucitons https://twiki.cern.ch/twiki/bin/viewauth/CMS/HowToGenXSecAnalyzer

Step 1: Setup proxy

```
voms-proxy-init -voms cms
```

Step 2: Setup CMSSW (any stable and compatible CMSSW should do)

```
cmsrel CMSSW_14_1_0_pre4
cd CMSSW_14_1_0_pre4/src
cmsenv
git cms-addpkg GeneratorInterface/Core
scram b -j8
cd ../../
```

Step3: Clone this repo
```
git clone https://github.com/green-cabbage/genproductions.git 
```

Sometimes cloning the full repo takes a long time or gets stuck, in which just a shallow clone would do:
```
git clone https://github.com/green-cabbage/genproductions.git  --depth=1
```

Step4: Use the XS calculation tool
```
cd genproductions/Utilities/calculateXSectionAndFilterEfficiency
./calculateXSectionAndFilterEfficiency.sh -f datasets.txt -c UL18 -d MINIAODSIM -n 1000000
```
```
# input parameters:
# -f wants the input file containing the list of dataset names (default) or McM prepID (requires -m)
# -c specifies the campaign, i.e. the string to be used to search for the secondary dataset name /.../*Moriond17*/*
# -d specifies the datatier to be used, i.e.  /.../*/MINIAODSIM
# -n number of events to be used for each dataset to compute the cross section
# -m use the McM prepID instead of the dataset names
```
NOTE: sometimes the bash script crashes without printing the error, in which case, go straight to the python scripts:

```
python3 compute_cross_section.py -c UL18 -f /CMSDAS/compliant/sample/path 
```

This python script above will print a cmsRun script, which you can just copy and paste it on the terminal and run it.




