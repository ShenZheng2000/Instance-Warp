# Instance Warping for Domain Adaptation

<br>_[Shen Zheng](https://shenzheng2000.github.io/), [Anurag Ghosh](https://anuragxel.github.io/), [Srinivasa Narasimhan](https://www.cs.cmu.edu/~srinivas/)_<br>
In Submission

# Overview

# 2PCNet for Object Detection

See [Night-Object-Detection/README.md](https://github.com/ShenZheng2000/Night-Object-Detection/blob/master/README.md)

# DAFormer for Semantic Segmentation

See [DAFormer/README.md](https://github.com/ShenZheng2000/DAFormer/blob/master/README.md)

# MIC for Semantic Segmentation

See [MIC/seg/README.md](https://github.com/ShenZheng2000/MIC/blob/master/seg/README.md)

# Get Started

Clone this repo recursively

```
git clone --recurse-submodules https://github.com/ShenZheng2000/Instance-Warp
```

# Image Warping Json Files

To train semantic segmentation models using our instance-level warping techinique,
you need to download the JSON file that contains the bounding boxes. Download link is [[here](https://drive.google.com/drive/folders/1kb1KwJLFhV0GpFtMhC_GedEnUxh_jy79?usp=sharing)]


# DAFormer and MIC: Environment Setup

<details>
  <summary><strong>Folder Preparation</strong></summary>

Run `mkdir outs` in the terminal to create a directory where logs from `nohup` can be saved.

</details>


<details>
  <summary><strong>Symbolic Link</strong></summary>

Assuming your current folder's ABSOLUTE path is `XXX`, proceed with the following steps to create a symbolic link for the code related to warping, which is necessary for semantic segmentation models, including DAFormer and MIC.

NOTE: Prior to advancing with semantic segmentation, ensure that the detection code is functioning properly.

```
ln -s XXX/Night-Object-Detection/twophase/data/transforms XXX/MIC/seg/mmseg/transforms
ln -s XXX/Night-Object-Detection/twophase/data/transforms XXX/DAFormer/mmseg/transforms
```

</details>


<details>
  <summary><strong>Build Environment</strong></summary>

For this project, we used python 3.7. We recommend setting up a new virtual
environment:

```shell
conda create -n CMDA python=3.7
conda activate CMDA
```

</details>


<details>
  <summary><strong>Install Packages</strong></summary>

In that environment, the requirements can be installed with:

```shell
pip install -r requirements.txt -f https://download.pytorch.org/whl/torch_stable.html
pip install kornia==0.5.8
pip install -U openmim
mim install mmcv-full==1.3.7
```

</details>


<details>
  <summary><strong>Download Weights</strong></summary>

Further, please download the MiT weights from [here](https://drive.google.com/file/d/1KuyZzBr2_HRG5L78Ki9YOe2pkd8wcETE/view?usp=drive_link)


All experiments were executed on a NVIDIA RTX 4090 Ti.

</details>


# DAFormer and MIC: Dataset Setup

<details>
  <summary><strong>Dataset Preparations</strong></summary>

**Cityscapes:** Please, download `leftImg8bit_trainvaltest.zip` and
`gt_trainvaltest.zip` from [here](https://www.cityscapes-dataset.com/downloads/)
and extract them to `$data_path/cityscapes`.

**Foggy Cityscapes:** Please, download `leftImg8bit_trainvaltest_foggy.zip` and
`gt_trainvaltest.zip` from [here](https://www.cityscapes-dataset.com/downloads/)
and extract them to `$data_path/foggy_cityscapes`.

**GTA:** Please, download all image and label packages from
[here](https://download.visinf.tu-darmstadt.de/data/from_games/) and extract
them to `$data_path/gta`.

**Synthia:** Please, download `SYNTHIA-RAND-CITYSCAPES` from
[here](http://synthia-dataset.net/downloads/) and extract it to `$data_path/synthia`.

**ACDC:** Please, download `rgb_anon_trainvaltest.zip` and
`gt_trainval.zip` from [here](https://acdc.vision.ee.ethz.ch/download) and
extract them to `$data_path/acdc`. Further, please restructure the folders from
`condition/split/sequence/` to `split/` using the following commands:

```shell
cd $data_path
rsync -a acdc/rgb_anon/*/train/*/* acdc/rgb_anon/train/
rsync -a acdc/rgb_anon/*/val/*/* acdc/rgb_anon/val/
rsync -a acdc/gt/*/train/*/*_labelTrainIds.png acdc/gt/train/
rsync -a acdc/gt/*/val/*/*_labelTrainIds.png acdc/gt/val/
```

**Dark Zurich:** Please, download the `Dark_Zurich_train_anon.zip`
and `Dark_Zurich_val_anon.zip` from
[here](https://www.trace.ethz.ch/publications/2019/GCMA_UIoU/) and extract it
to `$data_path/dark_zurich`.

</details>

<details>
  <summary><strong>Dataset Folder Structures</strong></summary>
  <pre>
DAFormer
├── ...
├── $data_path
│   ├── acdc
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
│   ├── dark_zurich
│   │   ├── gt
│   │   │   ├── val
│   │   ├── rgb_anon
│   │   │   ├── train
│   │   │   ├── val
│   ├── gta
│   │   ├── images
│   │   ├── labels
│   ├── synthia
│   │   ├── RGB
│   │   ├── GT
│   │   │   ├── LABELS
│   ├── foggy_cityscapes
│   │   ├── leftImg8bit_foggy
│   │   │   ├── train
│   │   │   ├── val
│   │   ├── gtFine
│   │   │   ├── train
│   │   │   ├── val
  </pre>
</details>


<details>
  <summary><strong>Data Preprocessing</strong></summary>

Finally, please run the following scripts to convert the label IDs to the
train IDs and to generate the class index for RCS:

```shell
python tools/convert_datasets/gta.py $data_path/gta --nproc 8
python tools/convert_datasets/cityscapes.py $data_path/cityscapes --nproc 8
python tools/convert_datasets/synthia.py $data_path/synthia/ --nproc 8
```

</details>


# DAFormer and MIC: Val Set Evaluation

Models can be tested after the training has finished:

```shell
sh test.sh path/to/checkpoint_directory
```


# DAFormer and MIC: Test Set Evaluation

The results for Cityscapes→ACDC and Cityscapes→DarkZurich are reported on
the test split of the target dataset. To generate the predictions for the test
set, please:

1. Generate test set predictions using:

```shell
bash test_test.sh path/to/checkpoint_directory
```

2. Submit to [ACDC](https://acdc.vision.ee.ethz.ch/submit) or [DarkZurich](https://codalab.lisn.upsaclay.fr/competitions/3783#participate-submit_results) public evaluation server to obtain the scores. 


# Utility Scripts

See utility scripts in [[here](https://github.com/ShenZheng2000/Instance-Warp-Scripts)]