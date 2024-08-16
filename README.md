# Compute_MCD

Mel Cepstral Distortion (MCD), which is used to assess the quality of the generated speech by comparing the discrepancy between generated and ground-truth speeches.

# Dependencies
- pyworld
- pysptk
- fastdtw
- librosa
- scipy

# Installation
The source file is in this repository and you can use pip to install pymcd:
```
pip install pymcd
```
This package (pymcd) computes Mel Cepstral Distortion (MCD) in python.

# Example
single voice comparison
```python
from pymcd.mcd import Calculate_MCD

# instance of MCD class
# three different modes "plain", "dtw" and "dtw_sl" for the above three MCD metrics
mcd_toolbox = Calculate_MCD(MCD_mode="plain")

# two inputs w.r.t. reference (ground-truth) and synthesized speeches, respectively
mcd_value = mcd_toolbox.calculate_mcd("001.wav", "002.wav")
```

batch voice comparison
```python
import glob
from pymcd.mcd import Calculate_MCD

vc_trg_refs = glob.glob('./data/official_stargan-vc/wavs/trg/*')
vc_conv_synths = glob.glob('./data/official_stargan-vc/wavs/conv/*')
vc2_trg_refs = glob.glob('./data/official_stargan-vc2/wavs/trg/*')
vc2_conv_synths = glob.glob('./data/official_stargan-vc2/wavs/conv/*')

def average_mcd(ref_mcep_files, synth_mcep_files):
    min_cost_tot = 0.0
    frames_tot = 0
    counts = len(ref_mcep_files)
    print(counts)
    sum = 0.0

    for ref in ref_mcep_files:
        for synth in synth_mcep_files:
            # print(os.path.basename(synth))

            # get the trg_ref and conv_synth speaker name and sample id
            # 文件命名中取 '_' 分隔的第一个和最后一个字符串作为匹配同一对目标-转换语音的唯一标识
            ref_fsplit, synth_fsplit = os.path.basename(ref).split('_'), os.path.basename(synth).split('_')
            ref_spk, ref_id = ref_fsplit[0], ref_fsplit[-1]
            synth_spk, synth_id = synth_fsplit[0], synth_fsplit[-1]

            # if the speaker name is the same and sample id is the same, do MCD
            if ref_spk == synth_spk and ref_id == synth_id:
                mcd_value = mcd_toolbox.calculate_mcd(ref, synth)
                print(mcd_value)
                sum += mcd_value

    return sum / counts

mcd_toolbox = Calculate_MCD(MCD_mode='dtw')
print(average_mcd(vc_trg_refs, vc_conv_synths))
print(average_mcd(vc2_trg_refs, vc2_conv_synths))
```

# Reference
https://pypi.org/project/pymcd/

https://github.com/SamuelBroughton/Mel-Cepstral-Distortion

https://github.com/MattShannon/mcd/tree/master

https://github.com/Lukelluke/MCD-MEL-CEPSTRAL-DISTANCE-MCD-application
