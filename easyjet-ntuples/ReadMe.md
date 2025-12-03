# EasyJet Ntuple Production

Before starting clone and install `easyjet`: [EasyJet](https://gitlab.cern.ch/easyjet/easyjet),

### EasyJet Installation 
```bash
#cd /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/Easyjet/
setupATLAS
lsetup git
git lfs install
git clone --recursive --no-checkout --origin upstream ssh://git@gitlab.cern.ch:7999/easyjet/easyjet.git
cd easyjet
```

How to compile and run

```bash
mkdir build
cd build
source ../easyjet/setup.sh
cmake ../easyjet/
make
source */setup.sh
cd ..
mkdir run
cd run
# Launch your favorite easyjet command
lsetup pyamivms
vms
lsetup panda
```
### EasyJet Ntuples Production For b-jets In-situ Calibration Study

In this part I will run easyjet for Xbb Calibration, but to consider in my case I need small-R jets, while in `XbbCalib` they didn't use them they only deal with large-R jets. 
Go to Analysis Package for the *$Xbb$ Calibration* and do some modification to include the small-r

- `datasets/`
    - `Zbbj`: Lists of data and mc samples to process
- `python/`: Main python code to configure the components (objects, selections as well as the variables to save)
  - `ZbbjCalib_config`
- `scripts`
  - `grid`: Scripts for submitting grid jobs (used Zbbj_MC_run3.sh and Zbbj_data_run3.sh) 
- `share/`: yaml files containing configurations used by the components
  - `RunConfig_ZbbjCalib_run3`: configurations called by the executables (see below);	
  - `trigger_Zbbj`: list of the triggers to use per year.
  - `XSectionsData`: XSections for computing weights.
- `src/`: C++ code
  - `ZbbjCalibSelectorAlg`: Find if the event pass the baseline selection for Zbbj calibration;
  - `BaselineVarsZbbjCalibAlg`: Compute the baseline variables for the Zbbj calibration.

The `ZbbjCalibSelectorAlg.h` modified to add small-r jets handeling 

```bash
From:
      m_lrjetHandle{ this, "largeRJets", "",   "Large-R jet container to read" };
To:
      CP::SysReadHandle<xAOD::JetContainer>
      m_jetHandle{ this, "jets", "XbbCalibJets_%SYS%",   "Jet container to read" };

      CP::SysReadHandle<xAOD::JetContainer>
      //m_lrjetHandle{ this, "largeRJets", "",   "Large-R jet container to read" };
      m_lrjetHandle{ this, "lrJets", "XbbCalibLRJets_%SYS%",   "Large-R jet container to read" };
```
Also add `ATH_CHECK (m_jetHandle.initialize(m_systematicsList));` in `ZbbjCalibSelectorAlg.cxx`


# Easyjet xbbcalib-ntupler Testing 

Before submitting a job on the grid download a sample and test it locally, for that you need to

```bash
setupATLAS 
lsetup rucio

rucio list-dids "mc23_13p6TeV.700855.Sh_2214_Zbb_ptZ_200_ECMS.deriv.DAOD_PHYS.*"

#list the differents files in the folder 
rucio list-files mc23_13p6TeV.700855.Sh_2214_Zbb_ptZ_200_ECMS.deriv.DAOD_PHYS.e8582_s4369_r16083_p7017

#Select once file and download it
rucio download mc23_13p6TeV:DAOD_PHYS.45320357._000007.pool.root.1

#run the job locally in lpnatlas:
xbbcalib-ntupler DAOD_PHYS.45320357._000007.pool.root --run-config ../easyjet/XbbCalib/share/RunConfig_ZbbjCalib_run3.yaml --out-file calibration-vars.root

#xbbcalib-ntupler mc23_13p6TeV.700855.Sh_2214_Zbb_ptZ_200_ECMS.deriv.DAOD_PHYS.e8514_s4159_r15224_p7017/DAOD_PHYS.46860364._000005.pool.root.1 --run-config ../easyjet/XbbCalib/share/RunConfig_ZbbjCalib_run3.yaml --out-file calibration-vars.root
```
If all fine and all the variables/informations are there, its time to lunch your jobs on the grid

Hence, before submitting the jobs make sure to source easyjet and to make build if you made any modification on your codes than go to run and `lsetup pyamivms`, `vms`, `lsetup panda`

```bash
source ../easyjet/XbbCalib/scripts/grid/Zbbj_MC_run3.sh
#source ../easyjet/XbbCalib/scripts/grid/Zbbj_data_run3.sh
```

If all works good you should see something kind of:

```bash
source ../easyjet/XbbCalib/scripts/grid/Zbbj_MC_run3.sh
INFO : archiving source files with cpack
INFO : the build directory is /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/Easyjet/build
INFO : archiving source files
INFO : archiving InstallArea
INFO : gathering files under /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/Easyjet/run/./
INFO : checking sandbox
prun --inDS mc23_13p6TeV.700855.Sh_2214_Zbb_ptZ_200_ECMS.deriv.DAOD_PHYS.e8514_s4159_r15224_p7017 --outDS user.tamezza.bjes_26Nov2025.700855.e8514_s4159_r15224_p7017 --notExpandInDS --exec xbbcalib-ntupler %IN -l --run-config config.yaml --out-file output-tree.root --outputs TREE:output-tree.root --writeInputToTxt IN:in.txt --useAthenaPackages --destSE GRIF_LOCALGROUPDISK --nGBPerJob 30 --framework easyjet --athenaTag AthAnalysis,25.2.75 --inTarBall code.tar.gz
INFO : upload sandbox
INFO : submit user.tamezza.bjes_26Nov2025.700855.e8514_s4159_r15224_p7017/
INFO : succeeded. new jediTaskID=47656196
```

once the jobs are done in [PanDa](https://bigpanda.cern.ch/), check task ID and name you see the status of the jobs
 
```bash

#/data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/samples_zbbj/Zbbj_bjes_27Nov2025
setupATLAS
lsetup rucio
#MC Signal Zbbjets
rucio list-dids "user.tamezza.bjes_27Nov2025.700855.%TREE"
rucio list-dids "user.tamezza.bjes_27Nov2025.700855.%TREE" | awk '{print $2}' > list_zbbj.txt
cat list_zbbj.txt
vim list_zbbj.txt
rucio download `cat list_zbbj.txt`

#MC Background Dijets
for i in {801165..801173}; do
    echo "Processing sample ${i}..."
    rucio list-dids "user.tamezza.bjes_27Nov2025.${i}.%TREE" | awk '{print $2}' > list_dijets.txt
done
#Or
rucio list-dids "user.tamezza.bjes_27Nov2025.8011*.%TREE" | awk '{print $2}' > list_dijets.txt

#Data
rucio list-dids "user.tamezza.bjes_27Nov2025.data*.%TREE"
rucio list-dids "user.tamezza.bjes_27Nov2025.data*.%TREE" | awk '{print $2}' > list_data.txt
```












