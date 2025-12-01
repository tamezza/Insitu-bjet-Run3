# Post process easyjet ntuples

```bash
#cd /HH-Analysis-bbbb/PostJetCalib/fastpostez
setup source.sh
setup compile.sh
run_bjes
# Check the config zbby_bjes_config.json
vim  zbbj_bjes_config.json

run_bjes -c zbbj_bjes_config.json -i /data/atlas/tamezza/BJetCalib_Run3/easyjet_ntuples/samples_zbbj/Zbbj_bjes_27Nov2025 -o /data/atlas/tamezza/BJetCalib_Run3/JetCalibProcess/Zbbj/Zbbj_xbbCalib -n 10

#Zbbj_xbbCalib_27Nov2025

``
