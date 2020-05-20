# diHiggsAnalysis

Batch-processing facility for Higgs Pair Production HH to bbZZ(2l2q) Ntuples to create light-weight trees, and analyzer facility for histograms, signal-background separation optimization, event-counting and limit-setting. Full Run II analysis using NanoAOD.

Vivan Nguyen (vivannguyen@cerh.ch) May 2020  
David Morse (dmorse@cern.ch) May 2015  
Darin Baumgartel (darinb@cern.ch) Feb 2014

Running Instructions: requires running on the CERN lxplus7 computing system

## Quick start

```bash
git clone git@github.com:vivannguyen/diHiggsAnalysis.git diHiggsAnalysis
cd diHiggsAnalysis

cmsrel CMSSW_10_2_18
cd CMSSW_10_2_18/src
cmsenv
scram b -j8

git checkout remotes/origin/developer
cd $CMSSW_BASE/src
scram b -j8

cd $CMSSW_BASE/diHiggsAnalysis

```

## Pileup-reweighting

Running the code to get the pileup-reweighting histograms and integrated-lumi info.

./GetPULumiInfo.tcsh

## NTuple Information

Organize your NTuples by creating a CSV file which contains the NTuple Information.

CSV files should have columns like :
SignalType,Xsections,N_orig,Group,CustomJson,EOSDirectory

- SignalType:    A unique identifier at the beginning of the names of the root files.
- Xsections:     The cross-section to normalize the sample to (NLO is better!)
- N_orig:        The original number of events in the sample... more on this in [STEP 4]
- Group:         The group for the files. For instance, you might have three SignalTypes like WW, WZ, and ZZ, and want to put them in a Group called "DiBoson"
- CustomJson:    The name of a Json file specifying the good runs/lumis to use. This can be the same for every data type, or different, or 0 for MC
- EOSDirectory:  The eos path where the files are kept for this signaltype. Should be like a typical EOS path e.g. /store/group/..../

Please see a convenient example:
NTupleInfo2017FullHH_stockNano.csv

## Event Counter

Get the original number of events for MC (to fill out the N_orig in the csv file) using the counting histograms in the ntuples. Can batch and gather the results:

```bash
python AnalyzerMakerFastLocalHTCondor.py -i NTupleInfo2017FullHH_stockNano.csv -py NTupleEvCounter.py -t EventCount_2017Full_stockNano -j Cert_294927-306462_13TeV_PromptReco_Collisions17_JSON.txt -p 0 -q espresso -s 100 --FileRefresh --merge
```

Arguments:

-i CSV File:     The CSV file you wish to run on  
-t Tag name:    Results will output in a directory specified by this tag-name  
-j JSON file:    Not important here, needed in making analysis trees
-p 0:            Not important here, needed in making analysis trees
-q 8nh:          The batch queue to use.  
-s 100:          Number of files to analyzer per batch job (the split number)  
--FileRefresh:   The code will automatically make a list of good ntuple files and store it locally for future use, so it doesn't have to re-read directories all the time. This demands to re-read directories.

## Make Analysis trees

```bash
python AnalyzerMakerFastLocalHTCondor.py -i NTupleInfo2017FullHH_stockNano.csv -py NTupleAnalyzerHH_nanoAOD.py -t Full2017QCDNonIsoQuickTest -j Cert_294927-306462_13TeV_PromptReco_Collisions17_JSON.txt -p 0 -q longlunch -s 20 -y 2017 --merge
```
