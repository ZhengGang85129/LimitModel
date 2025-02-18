
# Enviroment Setup
```
export SCRAM_ARCH=slc7_amd64_gcc700
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit

cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v8.2.0
scramv1 b clean; scramv1 b # always make a clean build
```

## Higgs Combination Tool Installation(Only For Raman) 
Follow the Higgs Combination Twiki to install the combine package and compile it, 
 (add the link to combine Twiki  here )
```
source envsetter.sh
```

### Install the code 
The code is part of the repository ttcbar, to get it simply do git clone: 
```
cd $CMSSW_BASE/src
git clone git@github.com:ZhengGang85129/LimitModel.git
```
### Rebin and merging of processes 

Move into LimitModel
```
cd LimitModel; cmsenv 
```

Once input from Meng/others are ready in the form of plain histograms, normalised to cross-section x Lumi, use the ReBin.py macro to perform two main tasks: 

1. Merge the histograms for various processes and make a new histogram which is sum of others, this is to make sure we don't have huge stats fluctuations. histograms for same/similar physics Processes are added. 

2. Once merging of histograms are done, each of these histogram is then rebinned, (uniform or non-uniform) depending on the needs. 

Ex: For coupling value 0.4 for all the dilepton channels in 2016postapv samples:

```
python ReBin.py -c all --Couplings 0p4 --y 2016postapv 

``` 
Ex: For coupings values from 0.1 to 1.0 for all the dilepton channels in 2016postapv samples:
```
python ReBin.py -c all --Couplings 0p1 0p4 0p8 1p0 --y 2016postapv --Masses 200 300 350 400 500 600 700 800 900 1000
```


You can see the rough description through the following commands
```
python ReBin.py --h
```


#Before running the macro, some values needs to be set, specially the input and output paths. 


The new output files are then used for the limit extraction. 

### Create template card for one region  datacards 
The next step is to create the datacards. The input needed for making datacards are; 
1. *.root file from previous step 
2. ttc.yaml file which has all the information about the nuisnaces and processes. 

The datacards for each decay mode, each year can be created using following syntax, just copy paste them to terminal and wait for it to be over. 

```
python prepareCards.py -y 2016apv -c em -reg 'SR_em'
python prepareCards.py -y 2016apv -c ee -reg 'SR_ee'
python prepareCards.py -y 2016apv -c mm -reg 'SR_mm'


python prepareCards.py -y 2016postapv -c em -reg 'SR_em'
python prepareCards.py -y 2016postapv -c ee -reg 'SR_ee'
python prepareCards.py -y 2016postapv -c mm -reg 'SR_mm'

python prepareCards.py -y 2017 -c em -reg 'SR_em'
python prepareCards.py -y 2017 -c ee -reg 'SR_ee'
python prepareCards.py -y 2017 -c mm -reg 'SR_mm'


python prepareCards.py -y 2018 -c em -reg 'SR_em'
python prepareCards.py -y 2018 -c ee -reg 'SR_ee'
python prepareCards.py -y 2018 -c mm -reg 'SR_mm'
```

### Combine the cards for all regions to make a jumbo card 
```
cd datacards_ttc_2016postapv; combineCards.py em=ttc_datacard_2016postapv_SR_em_em_template.txt ee=ttc_datacard_2016postapv_SR_ee_ee_template.txt mm=ttc_datacard_2016postapv_SR_mm_mm_template.txt > ttc_datacard_2016postapv_SR_C_C_template.txt ; cd -

cd datacards_ttc_2016apv; combineCards.py em=ttc_datacard_2016apv_SR_em_em_template.txt ee=ttc_datacard_2016apv_SR_ee_ee_template.txt mm=ttc_datacard_2016apv_SR_mm_mm_template.txt > ttc_datacard_2016apv_SR_C_C_template.txt ; cd -

cd datacards_ttc_2017; combineCards.py em=ttc_datacard_2017_SR_em_em_template.txt ee=ttc_datacard_2017_SR_ee_ee_template.txt mm=ttc_datacard_2017_SR_mm_mm_template.txt > ttc_datacard_2017_SR_C_C_template.txt ; cd -

cd datacards_ttc_2018; combineCards.py em=ttc_datacard_2018_SR_em_em_template.txt ee=ttc_datacard_2018_SR_ee_ee_template.txt mm=ttc_datacard_2018_SR_mm_mm_template.txt > ttc_datacard_2018_SR_C_C_template.txt ; cd -

cd datacards_ttc_run2; combineCards.py d2016=../datacards_ttc_2016/ttc_datacard_2016_SR_C_C_template.txt d2016apv=../datacards_ttc_2016apv/ttc_datacard_2016apv_SR_C_C_template.txt d2017=../datacards_ttc_2017/ttc_datacard_2017_SR_C_C_template.txt d2018=../datacards_ttc_2018/ttc_datacard_2018_SR_C_C_template.txt > ttc_datacard_run2_SR_C_C_template.txt; cd -
```

### Run the cards 

#### 2016postapv
```
python runlimits.py -c em --rtc rtc04 -y 2016postapv 
python runlimits.py -c mm --rtc rtc04 -y 2016postapv
python runlimits.py -c ee --rtc rtc04 -y 2016postapv
python runlimits.py -c C --rtc rtc04 -y 2016postapv
```

#### 2016 apv 
```
python runlimits.py -c em --rtc rtc04 -y 2016apv 
python runlimits.py -c mm --rtc rtc04 -y 2016apv
python runlimits.py -c ee --rtc rtc04 -y 2016apv
python runlimits.py -c C --rtc rtc04 -y 2016apv
```
#### 2017
```
python runlimits.py -c em --rtc rtc04 -y 2017 
python runlimits.py -c mm --rtc rtc04 -y 2017
python runlimits.py -c ee --rtc rtc04 -y 2017
python runlimits.py -c C --rtc rtc04 -y 2017
```
#### 2018
```
python runlimits.py -c em --rtc rtc04 -y 2018 --Masses 200 300 350 400 500 600 700 800 900 1000 
python runlimits.py -c mm --rtc rtc04 -y 2018 --Masses 200 300 350 400 500 600 700 800 900 1000 
python runlimits.py -c ee --rtc rtc04 -y 2018 --Masses 200 300 350 400 500 600 700 800 900 1000 
python runlimits.py -c C  --rtc rtc04 -y 2018 --Masses 200 300 350 400 500 600 700 800 900 1000 
```
```
python runlimits.py -c em --rtc rtc08 -y 2018 
python runlimits.py -c mm --rtc rtc08 -y 2018
python runlimits.py -c ee --rtc rtc08 -y 2018
python runlimits.py -c C --rtc rtc08 -y 2018
```


#### run 2 
```
python runlimits.py -c C --rtc rtc01 -y run2	
python runlimits.py -c C --rtc rtc04 -y run2	
python runlimits.py -c C --rtc rtc08 -y run2	
python runlimits.py -c C --rtc rtc10 -y run2	
```


```
python runlimits.py -c em --rtc rtc01 -y 2017 
python runlimits.py -c mm --rtc rtc01 -y 2017
python runlimits.py -c ee --rtc rtc01 -y 2017
python runlimits.py -c C --rtc rtc01 -y 2017
```
```
python runlimits.py -c em --rtc rtc08 -y 2017 
python runlimits.py -c mm --rtc rtc08 -y 2017
python runlimits.py -c ee --rtc rtc08 -y 2017
python runlimits.py -c C --rtc rtc08 -y 2017
```
```
python runlimits.py -c em --rtc rtc10 -y 2017 
python runlimits.py -c mm --rtc rtc10 -y 2017
python runlimits.py -c ee --rtc rtc10 -y 2017
python runlimits.py -c C --rtc rtc10 -y 2017
```
```
python runlimits.py -c em --rtc rtc01 
python runlimits.py -c mm --rtc rtc01
python runlimits.py -c ee --rtc rtc01
python runlimits.py -c C --rtc rtc01

```

```
python runlimits.py -c em --rtc rtc08
python runlimits.py -c mm --rtc rtc08
python runlimits.py -c ee --rtc rtc08
python runlimits.py -c C --rtc rtc08
```
```
python runlimits.py -c em --rtc rtc10
python runlimits.py -c mm --rtc rtc10
python runlimits.py -c ee --rtc rtc10
python runlimits.py -c C --rtc rtc10
```

## For impacts and pulls 
source runallchecks.sh SignalExtractionChecks2017 20161718 C datacards_ttc_run2/ttc_datacard_run2_SR_C_C_MA200_rtc04.txt 

source runallchecks.sh SignalExtractionChecks2017 2017 C datacards_ttc_2017/ttc_datacard_2017_SR_C_C_MA200_rtc04.txt
* autoMCStats 10 0 1


## For signal shape comparison 
python OverlappingPlots.py; cp -r plots_SignalShapeComparison/ /afs/cern.ch/work/k/khurana/public/AnalysisStuff/ttc


## for rescaling signal
sigscale rateParam * TAToTTQ_rtc01_MA350 0.01 [0.009999,0.01111]


## Stack plots 
python stackhist.py fitDiagnostics_C_20161718_asimov_t_0_SignalExtractionChecks2017.root ee asimov 20161718
python stackhist.py fitDiagnostics_C_20161718_asimov_t_0_SignalExtractionChecks2017.root em asimov 20161718
python stackhist.py fitDiagnostics_C_20161718_asimov_t_0_SignalExtractionChecks2017.root mm asimov 20161718

nuisance edit rename * * jes2016 jes2017
nuisance edit rename * * jes2018 jes2017
nuisance edit rename * * jes2016apv jes2017

page 124 of the AN for theory cross-section uncertainty 

## Trouble Shooting 

### For ReBin.py

If you encounter the error message like
```
Traceback (most recent call last):
  File "./ReBin.py", line 142, in <module>
    h_ttV.Add( f_in.Get(prefix+"ttZtoQQ"+inuis) )
TypeError: none of the 3 overloaded methods succeeded. Full details:
  bool TH1::Add(TF1* h1, double c1 = 1, const char* option = "") =>
    could not convert argument 1
  bool TH1::Add(const TH1* h1, double c1 = 1) =>
    could not convert argument 1
  bool TH1::Add(const TH1* h, const TH1* h2, double c1 = 1, double c2 = 1) =>
    takes at least 2 arguments (1 given)
```

You should tune the name of histogram you want to "get" and "rebin", instead of "naming", to be consistent with the histograms names in input file.
For this case, ttZtoQQ->ttZToQQ in line 142 for year2016postapv.

### For runlimits.py

If you encounter the error message like
```
Traceback (most recent call last):
  File "/afs/cern.ch/user/z/zhenggan/work_space/CMSSW_10_2_13/bin/slc7_amd64_gcc700/text2workspace.py", line 63, in <module>
    MB.doModel(justCheckPhysicsModel=options.justCheckPhysicsModel)
  File "/afs/cern.ch/user/z/zhenggan/work_space/CMSSW_10_2_13/python/HiggsAnalysis/CombinedLimit/ModelTools.py", line 110, in doModel
    if not justCheckPhysicsModel: self.doObservables()
  File "/afs/cern.ch/user/z/zhenggan/work_space/CMSSW_10_2_13/python/HiggsAnalysis/CombinedLimit/ShapeTools.py", line 61, in doObservables
    self.prepareAllShapes();
  File "/afs/cern.ch/user/z/zhenggan/work_space/CMSSW_10_2_13/python/HiggsAnalysis/CombinedLimit/ShapeTools.py", line 332, in prepareAllShapes
    shape = self.getShape(b,p); norm = 0;
  File "/afs/cern.ch/user/z/zhenggan/work_space/CMSSW_10_2_13/python/HiggsAnalysis/CombinedLimit/ShapeTools.py", line 573, in getShape
    raise RuntimeError, "Failed to find %s in file %s (from pattern %s, %s)" % (objname,finalNames[0],names[1],names[0])
RuntimeError: Failed to find ttc2016postapv_tzq in file FinalInputs/2016postapv/ttc_a_rtc04_MA500/TMVApp_500_em.root (from pattern ttc2016postapv_$PROCESS, FinalInputs/2016postapv/ttc_a_rtc04_MA500/TMVApp_500_em.root)
Error when running the combination:
	Failed to convert the input datacard from LandS to RooStats format. The lines above probably contain more information about the error.
combine -M AsymptoticLimits datacards_ttc_2016postapv/ttc_datacard_2016postapv_SR_em_em_MA500_rtc04.txt -t -1 > datacards_ttc_2016postapv/log/ttc_datacard_2016postapv_SR_em_em_MA500_rtc04.log
allparameters: ('500', '04')
04 500
```

You should tune the name of histogram you want to "get" and "rebin", instead of "naming",to be consistent with the histograms names in input file.

For this case, tzq -> tZq for year2016postapv while in processing ReBin.py.



## limit model for 1 year is done now. 
## nuisnace shapes used for the fitting 
## Signal shape comparison 
## post fit plots 
## combination for full Run 2 
## fit diagnostics test, mainly pulls, impacts and goodness of fit for the model. 
## add the interference samples to see the results. 
## perform 2d scans.  
