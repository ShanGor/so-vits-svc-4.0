# SoftVC VITS Singing Voice Conversion

[**English**](./README.md) | [**中文简体**](./README_zh_CN.md)

#### ✨ A fork with a greatly improved interface: [34j/so-vits-svc-fork](https://github.com/34j/so-vits-svc-fork)

#### ✨ A client supports real-time conversion: [w-okada/voice-changer](https://github.com/w-okada/voice-changer)

## Warning!!!

This project is an open source, offline project, and all members of SvcDevelopTeam and all developers and maintainers of this project (hereinafter referred to as contributors) have no control over this project.  The contributor of this project has never provided any organization or individual with any form of assistance, including but not limited to data set extraction, data set processing, computing support, training support, infering, etc.  Contributors to the project do not and cannot know what users are using the project for.  Therefore, all AI models and synthesized audio based on the training of this project have nothing to do with the contributors of this project.  All problems arising therefrom shall be borne by the user.

## 📏 Terms of Use

# Warning: Please solve the authorization problem of the dataset on your own. You shall be solely responsible for any problems caused by the use of non-authorized datasets for training and all consequences thereof.The repository and its maintainer, svc develop team, have nothing to do with the consequences!

1. This project is established for academic exchange purposes only and is intended for communication and learning purposes. It is not intended for production environments. 
2. Any videos based on sovits that are published on video platforms must clearly indicate in the description that they are used for voice changing and specify the input source of the voice or audio, for example, using videos or audios published by others and separating the vocals as input source for conversion, which must provide clear original video or music links. If your own voice or other synthesized voices from other commercial vocal synthesis software are used as the input source for conversion, you must also explain it in the description.
3. You shall be solely responsible for any infringement problems caused by the input source. When using other commercial vocal synthesis software as input source, please ensure that you comply with the terms of use of the software. Note that many vocal synthesis engines clearly state in their terms of use that they cannot be used for input source conversion.
4. It is forbidden to use the project to engage in illegal activities, religious and political activities. The project developers firmly resist the above activities. If they do not agree with this article, the use of the project is prohibited.
5. Continuing to use this project is deemed as agreeing to the relevant provisions stated in this repository README. This repository README has the obligation to persuade, and is not responsible for any subsequent problems that may arise.
6. If you use this project for any other plan, please contact and inform the author of this repository in advance. Thank you very much.

## 🆕 Update!

> Updated the 4.0-v2 model, the entire process is the same as 4.0. Compared to 4.0, there is some improvement in certain scenarios, but there are also some cases where it has regressed. Please refer to the [4.0-v2 branch](https://github.com/svc-develop-team/so-vits-svc/tree/4.0-v2) for more information.

## 📝 4.0 Feature list of branches

| Branch        |     Feature      |  whether compatible with the main branch model |
| :-------------: | :----------: | :------------:    |
| 4.0              |   main branch   |        -     |
| 4.0v2        |  The VISinger2 model is used  |        incompatibility     |
| 4.0-Vec768-Layer12    |  The feature input is the Layer 12 Transformer output of the Content Vec  |       incompatibility     |

## 📝 Model Introduction

The singing voice conversion model uses SoftVC content encoder to extract source audio speech features, then the vectors are directly fed into VITS instead of converting to a text based intermediate; thus the pitch and intonations are conserved. Additionally, the vocoder is changed to [NSF HiFiGAN](https://github.com/openvpi/DiffSinger/tree/refactor/modules/nsf_hifigan) to solve the problem of sound interruption.

### 🆕 4.0 Version Update Content

- Feature input is changed to [Content Vec](https://github.com/auspicious3000/contentvec)
- The sampling rate is unified to use 44100Hz
- Due to the change of hop size and other parameters, as well as the streamlining of some model structures, the required GPU memory for inference is **significantly reduced**. The 44kHz GPU memory usage of version 4.0 is even smaller than the 32kHz usage of version 3.0.
- Some code structures have been adjusted
- The dataset creation and training process are consistent with version 3.0, but the model is completely non-universal, and the data set needs to be fully pre-processed again.
- Added an option 1: automatic pitch prediction for vc mode, which means that you don't need to manually enter the pitch key when converting speech, and the pitch of male and female voices can be automatically converted. However, this mode will cause pitch shift when converting songs.
- Added option 2: reduce timbre leakage through k-means clustering scheme, making the timbre more similar to the target timbre.
- Added option 3: Added [NSF-HIFIGAN Enhancer](https://github.com/yxlllc/DDSP-SVC), which has certain sound quality enhancement effect on some models with few train-sets, but has negative effect on well-trained models, so it is closed by default
  
## 💬 About Python Version

After conducting tests, we believe that the project runs stably on `Python 3.8.9`.

## 📥 Pre-trained Model Files

#### **Required**

- ContentVec: [checkpoint_best_legacy_500.pt](https://ibm.box.com/s/z1wgl1stco8ffooyatzdwsqn2psd9lrr)
  - Place it under the `hubert` directory

```shell
# contentvec
wget -P hubert/ http://obs.cstcloud.cn/share/obs/sankagenkeshi/checkpoint_best_legacy_500.pt
# Alternatively, you can manually download and place it in the hubert directory
```

#### **Optional(Strongly recommend)**

- Pre-trained model files: `G_0.pth` `D_0.pth`
  - Place them under the `logs/44k` directory

Get them from svc-develop-team(TBD) or anywhere else.

Although the pretrained model generally does not cause any copyright problems, please pay attention to it. For example, ask the author in advance, or the author has indicated the feasible use in the description clearly.

#### **Optional(Select as Required)**

If you are using the NSF-HIFIGAN enhancer, you will need to download the pre-trained NSF-HIFIGAN model, or not if you do not need it.

- Pre-trained NSF-HIFIGAN Vocoder: [nsf_hifigan_20221211.zip](https://github.com/openvpi/vocoders/releases/download/nsf-hifigan-v1/nsf_hifigan_20221211.zip)
  - Unzip and place the four files under the `pretrain/nsf_hifigan` directory

```shell
# nsf_hifigan
https://github.com/openvpi/vocoders/releases/download/nsf-hifigan-v1/nsf_hifigan_20221211.zip
# Alternatively, you can manually download and place it in the pretrain/nsf_hifigan directory
# URL：https://github.com/openvpi/vocoders/releases/tag/nsf-hifigan-v1
```

## 📊 Dataset Preparation

Simply place the dataset in the `dataset_raw` directory with the following file structure.

```
dataset_raw
├───speaker0
│   ├───xxx1-xxx1.wav
│   ├───...
│   └───Lxx-0xx8.wav
└───speaker1
    ├───xx2-0xxx2.wav
    ├───...
    └───xxx7-xxx007.wav
```

You can customize the speaker name.

```
dataset_raw
└───suijiSUI
    ├───1.wav
    ├───...
    └───25788785-20221210-200143-856_01_(Vocals)_0_0.wav
```

## 🛠️ Preprocessing

### 0. Slice audio

Slice to `5s - 15s`, a bit longer is no problem. Too long may lead to `torch.cuda.OutOfMemoryError` during training or even pre-processing.

By using [audio-slicer-GUI](https://github.com/flutydeer/audio-slicer) or [audio-slicer-CLI](https://github.com/openvpi/audio-slicer)

In general, only the `Minimum Interval` needs to be adjusted. For statement audio it usually remains default. For singing audio it can be adjusted to `100` or even `50`.

After slicing, delete audio that is too long and too short.

### 1. Resample to 44100Hz and mono

```shell
python resample.py
```

### 2. Automatically split the dataset into training and validation sets, and generate configuration files.

```shell
python preprocess_flist_config.py
```

### 3. Generate hubert and f0

```shell
python preprocess_hubert_f0.py
```

After completing the above steps, the dataset directory will contain the preprocessed data, and the dataset_raw folder can be deleted.

#### You can modify some parameters in the generated config.json

* `keep_ckpts`: Keep the last `keep_ckpts` models during training. Set to `0` will keep them all. Default is `3`.

* `all_in_mem`: Load all dataset to RAM. It can be enabled when the disk IO of some platforms is too low and the system memory is **much larger** than your dataset.

## 🏋️‍♀️ Training
About batch size in the config file, if you have 6G vram, suggest input 2-3, if you have 8G vram, might input 4-6. 
```shell
python train.py -c configs/config.json -m 44k
```

## 🤖 Inference

Use [inference_main.py](https://github.com/svc-develop-team/so-vits-svc/blob/4.0/inference_main.py)

```shell
# Example
python inference_main.py -m "logs/44k/G_30400.pth" -c "configs/config.json" -n "君の知らない物語-src.wav" -t 0 -s "nen"
```

Required parameters:
- `-m` | `--model_path`: path to the model.
- `-c` | `--config_path`: path to the configuration file.
- `-n` | `--clean_names`: a list of wav file names located in the raw folder.
- `-t` | `--trans`: pitch adjustment, supports positive and negative (semitone) values.
- `-s` | `--spk_list`: target speaker name for synthesis.
- `-cl` | `--clip`: voice forced slicing, set to 0 to turn off(default), duration in seconds.

Optional parameters: see the next section
- `-lg` | `--linear_gradient`: The cross fade length of two audio slices in seconds. If there is a discontinuous voice after forced slicing, you can adjust this value. Otherwise, it is recommended to use the default value of 0.
- `-fmp` | `--f0_mean_pooling`: Apply mean filter (pooling) to f0, which may improve some hoarse sounds. Enabling this option will reduce inference speed.
- `-a` | `--auto_predict_f0`: automatic pitch prediction for voice conversion, do not enable this when converting songs as it can cause serious pitch issues.
- `-cm` | `--cluster_model_path`: path to the clustering model, fill in any value if clustering is not trained.
- `-cr` | `--cluster_infer_ratio`: proportion of the clustering solution, range 0-1, fill in 0 if the clustering model is not trained.
- `-eh` | `--enhance`: Whether to use NSF_HIFIGAN enhancer, this option has certain effect on sound quality enhancement for some models with few training sets, but has negative effect on well-trained models, so it is turned off by default.

## 🤔 Optional Settings

If the results from the previous section are satisfactory, or if you didn't understand what is being discussed in the following section, you can skip it, and it won't affect the model usage. (These optional settings have a relatively small impact, and they may have some effect on certain specific data, but in most cases, the difference may not be noticeable.)

### Automatic f0 prediction

During the 4.0 model training, an f0 predictor is also trained, which can be used for automatic pitch prediction during voice conversion. However, if the effect is not good, manual pitch prediction can be used instead. But please do not enable this feature when converting singing voice as it may cause serious pitch shifting!
- Set `auto_predict_f0` to true in inference_main.

### Cluster-based timbre leakage control

Introduction: The clustering scheme can reduce timbre leakage and make the trained model sound more like the target's timbre (although this effect is not very obvious), but using clustering alone will lower the model's clarity (the model may sound unclear). Therefore, this model adopts a fusion method to linearly control the proportion of clustering and non-clustering schemes. In other words, you can manually adjust the ratio between "sounding like the target's timbre" and "being clear and articulate" to find a suitable trade-off point.

The existing steps before clustering do not need to be changed. All you need to do is to train an additional clustering model, which has a relatively low training cost.

- Training process:
  - Train on a machine with good CPU performance. According to my experience, it takes about 4 minutes to train each speaker on a Tencent Cloud machine with 6-core CPU.
  - Execute `python cluster/train_cluster.py`. The output model will be saved in `logs/44k/kmeans_10000.pt`.
- Inference process:
  - Specify `cluster_model_path` in `inference_main.py`.
  - Specify `cluster_infer_ratio` in `inference_main.py`, where `0` means not using clustering at all, `1` means only using clustering, and usually `0.5` is sufficient.

### F0 mean filtering

Introduction: The mean filtering of F0 can effectively reduce the hoarse sound caused by the predicted fluctuation of pitch (the hoarse sound caused by reverb or harmony can not be eliminated temporarily). This function has been greatly improved on some songs. However, some songs are out of tune. If the song appears dumb after reasoning, it can be considered to open.

- Set `f0_mean_pooling` to true in `inference_main.py`

### [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/justinjohn0306/so-vits-svc-4.0/blob/4.0/notebooks/sovits4_colab.ipynb) [sovits4_for_colab.ipynb](https://colab.research.google.com/github/justinjohn0306/so-vits-svc-4.0/blob/4.0/notebooks/sovits4_colab.ipynb)

**[23/03/16] No longer need to download hubert manually**

**[23/04/14] Support NSF_HIFIGAN enhancer**

## 📤 Exporting to Onnx

Use [onnx_export.py](https://github.com/svc-develop-team/so-vits-svc/blob/4.0/onnx_export.py)

- Create a folder named `checkpoints` and open it
- Create a folder in the `checkpoints` folder as your project folder, naming it after your project, for example `aziplayer`
- Rename your model as `model.pth`, the configuration file as `config.json`, and place them in the `aziplayer` folder you just created
- Modify `"NyaruTaffy"` in `path = "NyaruTaffy"` in [onnx_export.py](https://github.com/svc-develop-team/so-vits-svc/blob/4.0/onnx_export.py) to your project name, `path = "aziplayer"`
- Run [onnx_export.py](https://github.com/svc-develop-team/so-vits-svc/blob/4.0/onnx_export.py)
- Wait for it to finish running. A `model.onnx` will be generated in your project folder, which is the exported model.

### UI support for Onnx models

- [MoeSS](https://github.com/NaruseMioShirakana/MoeSS)
  - [Hubert4.0](https://huggingface.co/NaruseMioShirakana/MoeSS-SUBModel)

Note: For Hubert Onnx models, please use the models provided by MoeSS. Currently, they cannot be exported on their own (Hubert in fairseq has many unsupported operators and things involving constants that can cause errors or result in problems with the input/output shape and results when exported.)

CppDataProcess are some functions to preprocess data used in MoeSS

## ☀️ Previous contributors

For some reason the author deleted the original repository. Because of the negligence of the organization members, the contributor list was cleared because all files were directly reuploaded to this repository at the beginning of the reconstruction of this repository. Now add a previous contributor list to README.md.

*Some members have not listed according to their personal wishes.*

<table>
  <tr>
    <td align="center"><a href="https://github.com/MistEO"><img src="https://avatars.githubusercontent.com/u/18511905?v=4" width="100px;" alt=""/><br /><sub><b>MistEO</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/XiaoMiku01"><img src="https://avatars.githubusercontent.com/u/54094119?v=4" width="100px;" alt=""/><br /><sub><b>XiaoMiku01</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/ForsakenRei"><img src="https://avatars.githubusercontent.com/u/23041178?v=4" width="100px;" alt=""/><br /><sub><b>しぐれ</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/TomoGaSukunai"><img src="https://avatars.githubusercontent.com/u/25863522?v=4" width="100px;" alt=""/><br /><sub><b>TomoGaSukunai</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/Plachtaa"><img src="https://avatars.githubusercontent.com/u/112609742?v=4" width="100px;" alt=""/><br /><sub><b>Plachtaa</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/zdxiaoda"><img src="https://avatars.githubusercontent.com/u/45501959?v=4" width="100px;" alt=""/><br /><sub><b>zd小达</b></sub></a><br /></td>
    <td align="center"><a href="https://github.com/Archivoice"><img src="https://avatars.githubusercontent.com/u/107520869?v=4" width="100px;" alt=""/><br /><sub><b>凍聲響世</b></sub></a><br /></td>
  </tr>
</table>

## 📚 Some legal provisions for reference

#### Any country, region, organization, or individual using this project must comply with the following laws.

#### 《民法典》

##### 第一千零一十九条 

任何组织或者个人不得以丑化、污损，或者利用信息技术手段伪造等方式侵害他人的肖像权。未经肖像权人同意，不得制作、使用、公开肖像权人的肖像，但是法律另有规定的除外。未经肖像权人同意，肖像作品权利人不得以发表、复制、发行、出租、展览等方式使用或者公开肖像权人的肖像。对自然人声音的保护，参照适用肖像权保护的有关规定。

#####  第一千零二十四条 

【名誉权】民事主体享有名誉权。任何组织或者个人不得以侮辱、诽谤等方式侵害他人的名誉权。  

#####  第一千零二十七条

【作品侵害名誉权】行为人发表的文学、艺术作品以真人真事或者特定人为描述对象，含有侮辱、诽谤内容，侵害他人名誉权的，受害人有权依法请求该行为人承担民事责任。行为人发表的文学、艺术作品不以特定人为描述对象，仅其中的情节与该特定人的情况相似的，不承担民事责任。  

#### 《[中华人民共和国宪法](http://www.gov.cn/guoqing/2018-03/22/content_5276318.htm)》

#### 《[中华人民共和国刑法](http://gongbao.court.gov.cn/Details/f8e30d0689b23f57bfc782d21035c3.html?sw=%E4%B8%AD%E5%8D%8E%E4%BA%BA%E6%B0%91%E5%85%B1%E5%92%8C%E5%9B%BD%E5%88%91%E6%B3%95)》

#### 《[中华人民共和国民法典](http://gongbao.court.gov.cn/Details/51eb6750b8361f79be8f90d09bc202.html)》

## 💪 Thanks to all contributors for their efforts
<a href="https://github.com/svc-develop-team/so-vits-svc/graphs/contributors" target="_blank">
  <img src="https://contrib.rocks/image?repo=svc-develop-team/so-vits-svc" />
</a>
