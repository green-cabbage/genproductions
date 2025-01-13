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

An example could be:
```
python3 compute_cross_section.py -c UL18 -f /DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8/RunIISummer20UL17MiniAODv2-106X_mc2017_realistic_v9-v2/MINIAODSIM
```

Which will prompt you to run 
```
cmsRun genXsec_cfg.py inputFiles="/A very long list of root files"
```

Which after running that command on the same terminal, it will print something like
```
------------------------------------
GenXsecAnalyzer:
------------------------------------
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 
Overall cross-section summary 
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Process		xsec_before [pb]		passed	nposw	nnegw	tried	nposw	nnegw 	xsec_match [pb]			accepted [%]	 event_eff [%]
0		6.221e+03 +/- 1.207e+01		656590	592757	63833	747429	682744	64685	5.324e+03 +/- 1.071e+01		85.6 +/- 0.0	87.8 +/- 0.0
1		3.590e+03 +/- 1.172e+01		219704	159650	60054	1029493	695933	333560	9.866e+02 +/- 5.265e+00		27.5 +/- 0.1	21.3 +/- 0.0
2		1.671e+03 +/- 1.084e+01		123710	80376	43334	853958	512945	341013	3.599e+02 +/- 3.980e+00		21.5 +/- 0.2	14.5 +/- 0.0
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 
Total		1.148e+04 +/- 2.001e+01		1000004	832783	167221	2630880	1891622	739258	6.631e+03 +/- 1.433e+01		57.8 +/- 0.1	38.0 +/- 0.0
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Before matching: total cross section = 1.148e+04 +- 2.001e+01 pb
After matching: total cross section = 6.631e+03 +- 1.433e+01 pb
Matching efficiency = 0.4 +/- 0.0   [TO BE USED IN MCM]
Filter efficiency (taking into account weights)= (1.76623e+10) / (1.76623e+10) = 1.000e+00 +- 0.000e+00
Filter efficiency (event-level)= (1e+06) / (1e+06) = 1.000e+00 +- 0.000e+00    [TO BE USED IN MCM]

After filter: final cross section = 6.631e+03 +- 1.433e+01 pb
After filter: final fraction of events with negative weights = 1.672e-01 +- 2.570e-05
After filter: final equivalent lumi for 1M events (1/fb) = 6.680e-02 +- 1.061e-04

```

and here the Cross section value is represented as "After filter: final cross section", which in our case is 6.631e+03 +- 1.433e+01 pb
