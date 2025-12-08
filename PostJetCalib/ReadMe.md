# Post process easyjet ntuples

```bash
#cd /HH-Analysis-bbbb/PostJetCalib/fastpostez
setup source.sh
setup compile.sh
run_bjes
# Check the config zbby_bjes_config.json
vim  zbbj_bjes_config.json

#Sig
run_bjes -c zbbj_bjes_config.json -i /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/samples_zbbj/Zbbj_bjes_27Nov2025 -o /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Zbbj_xbbCalib -n 10

#Zbbj_xbbCalib_27Nov2025

#Bkg
run_bjes -c zbbj_bjes_config.json -i /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/samples_zbbj/Dijets_bjes_27Nov2025 -o /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Dijets_xbbCalib -n 10

#Data
run_bjes -c zbbj_bjes_config.json -i /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/samples_zbbj/Data_bjes_27Nov2025 -o /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Data_xbbCalib -n 2

Data_bjes_27Nov2025
```
Once done we must to merge the samples:

```bash
./merge_zbbj_data.sh /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Dijets_xbbCalib_2025-12-03_15-51-48  /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Multijets_xbbCalib

./merge_zbbj_data.sh /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Data_xbbCalib_2025-12-03_19-57-15  /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Data_xbbCalib__27Nov2025
```

Now its time to run insitu calib

```bash
#/data/atlas/tamezza/BJetCalib_Run3/BJetInsitu/Zbbj

```
