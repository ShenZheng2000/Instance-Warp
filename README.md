# Instance-Warp



Shen Zheng, Anurag Ghosh, Srinivasa Narasimhan

# Overview

# 2PCNet for Object Detection

See [Night-Object-Detection/README.md](https://github.com/ShenZheng2000/Night-Object-Detection/blob/master/README.md)

# DAFormer for Semantic Segmentation

See [DAFormer/README.md](https://github.com/ShenZheng2000/DAFormer/blob/master/README.md)

# MIC for Semantic Segmentation

See [MIC/seg/README.md](https://github.com/ShenZheng2000/MIC/blob/master/seg/README.md)


# Image Warping Code Setuup 

NOTE: Prior to advancing with semantic segmentation, ensure that the detection code is functioning properly.

Assuming your current folder's absolute path is `XXX`, proceed with the following steps to create a symbolic link for the code related to warping, which is necessary for semantic segmentation models, including DAFormer and MIC.

```
ln -s XXX/Night-Object-Detection/twophase/data/transforms XXX/MIC/seg/mmseg/transforms
ln -s XXX/Night-Object-Detection/twophase/data/transforms XXX/DAFormer/mmseg/transforms
```

# Get Started

Clone this repo recursively

```
git clone --recurse-submodules https://github.com/ShenZheng2000/Instance-Warp
```

# Environment Setup for DAFormer and MIC

For this project, we used python 3.7. We recommend setting up a new virtual
environment:

```shell
conda create -n CMDA python=3.7
conda activate CMDA
```

In that environment, the requirements can be installed with:

```shell
pip install -r requirements.txt -f https://download.pytorch.org/whl/torch_stable.html
pip install kornia==0.5.8
pip install -U openmim
mim install mmcv-full==1.3.7
```

Further, please download the MiT weights from [here](https://drive.google.com/file/d/1KuyZzBr2_HRG5L78Ki9YOe2pkd8wcETE/view?usp=drive_link)

<!-- ```shell
sh tools/download_checkpoints.sh
``` -->



All experiments were executed on a NVIDIA RTX 4090 Ti.


# Dataset Setup for DAFormer and MIC

**Cityscapes:** Please, download leftImg8bit_trainvaltest.zip and
gt_trainvaltest.zip from [here](https://www.cityscapes-dataset.com/downloads/)
and extract them to `$data_path/cityscapes`.

**GTA:** Please, download all image and label packages from
[here](https://download.visinf.tu-darmstadt.de/data/from_games/) and extract
them to `$data_path/gta`.

**Synthia (Optional):** Please, download SYNTHIA-RAND-CITYSCAPES from
[here](http://synthia-dataset.net/downloads/) and extract it to `$data_path/synthia`.

**ACDC (Optional):** Please, download rgb_anon_trainvaltest.zip and
gt_trainval.zip from [here](https://acdc.vision.ee.ethz.ch/download) and
extract them to `$data_path/acdc`. Further, please restructure the folders from
`condition/split/sequence/` to `split/` using the following commands:

```shell
cd $data_path
rsync -a acdc/rgb_anon/*/train/*/* acdc/rgb_anon/train/
rsync -a acdc/rgb_anon/*/val/*/* acdc/rgb_anon/val/
rsync -a acdc/gt/*/train/*/*_labelTrainIds.png acdc/gt/train/
rsync -a acdc/gt/*/val/*/*_labelTrainIds.png acdc/gt/val/
```

**Dark Zurich (Optional):** Please, download the Dark_Zurich_train_anon.zip
and Dark_Zurich_val_anon.zip from
[here](https://www.trace.ethz.ch/publications/2019/GCMA_UIoU/) and extract it
to `$data_path/dark_zurich`.

The final folder structure should look like this:

```none
DAFormer
├── ...
├── $data_path
│   ├── acdc (optional)
│   │   ├── gt
│   │   │   ├── train
│   │   │   ├── val
│   │   ├── rgb_anon
│   │   │   ├── train
│   │   │   ├── val
│   ├── cityscapes
│   │   ├── leftImg8bit
│   │   │   ├── train
│   │   │   ├── val
│   │   ├── gtFine
│   │   │   ├── train
│   │   │   ├── val
│   ├── dark_zurich (optional)
│   │   ├── gt
│   │   │   ├── val
│   │   ├── rgb_anon
│   │   │   ├── train
│   │   │   ├── val
│   ├── gta
│   │   ├── images
│   │   ├── labels
│   ├── synthia (optional)
│   │   ├── RGB
│   │   ├── GT
│   │   │   ├── LABELS
├── ...
```

**Data Preprocessing:** Finally, please run the following scripts to convert the label IDs to the
train IDs and to generate the class index for RCS:

```shell
python tools/convert_datasets/gta.py $data_path/gta --nproc 8
python tools/convert_datasets/cityscapes.py $data_path/cityscapes --nproc 8
python tools/convert_datasets/synthia.py $data_path/synthia/ --nproc 8
```


# Evaluation for DAFormer and MIC

Models can be tested after the training has finished:

```shell
sh test.sh path/to/checkpoint_directory
```

When evaluating a model trained on Synthia→Cityscapes, please note that the
evaluation script calculates the mIoU for all 19 Cityscapes classes. However,
Synthia contains only labels for 16 of these classes. Therefore, it is a common
practice in UDA to report the mIoU for Synthia→Cityscapes only on these 16
classes. As the Iou for the 3 missing classes is 0, you can do the conversion
mIoU16 = mIoU19 * 19 / 16.

The results for Cityscapes→ACDC and Cityscapes→DarkZurich are reported on
the test split of the target dataset. To generate the predictions for the test
set, please run:

```shell
python -m tools.test path/to/config_file path/to/checkpoint_file --test-set --format-only --eval-option imgfile_prefix=labelTrainIds to_label_id=False
```

The predictions can be submitted to the [ACDC](https://acdc.vision.ee.ethz.ch/submit) or [DarkZurich](https://codalab.lisn.upsaclay.fr/competitions/3783#participate-submit_results) public evaluation server of the
respective dataset to obtain the test score.



# TODO_List

* Add ID or config for warping-related experiments.
* Upload checkpoints for major experiments