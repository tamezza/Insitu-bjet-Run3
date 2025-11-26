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



