<a name="readme-top"></a>
<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/paplhjak/Facial-Age-Estimation-Benchmark">
  </a>

<h1 align="center">Databases of Facial Age Estimation Benchmark</h3>
  <p align="center">
    <a href="https://github.com/paplhjak/Facial-Age-Estimation-Benchmark/issues">Report Bug</a>
    ·
    <a href="https://github.com/paplhjak/Facial-Age-Estimation-Benchmark/issues">Request Feature</a>    
  </p>
</div>

<div>
  <p align="center">
  arXiv: <a href="https://arxiv.org/abs/2307.04570">A Call to Reflect on Evaluation Practices for Age Estimation: Comparative Analysis of the State-of-the-Art and a Unified Benchmark</a>
  </p>
</div>

This document explains how to replicate the data splits used in the paper: <a href="https://arxiv.org/abs/2307.04570">A Call to Reflect on Evaluation Practices for Age Estimation: Comparative Analysis of the State-of-the-Art and a Unified Benchmark</a>.

This repository is a submodule of ["Facial-Age-Estimation-Benchmark"](https://github.com/paplhjak/Facial-Age-Estimation-Benchmark).

**Important:** Due to limitations of git LFS, we recommend downloading the files from https://paplham.cloud/index.php/s/KSkaSPF7Btay2cb

## Prerequisites

In order to replicate our data splits, we need to understand the structure of two file types, `databases` and `benchmarks`.

A `database` file define a unified representation of a dataset.
A `benchmark` file defines how to use the samples from (possibly multiple) `databases`. I.e., `benchmark` defines which data are used for training, which are used for validation and for testing.

The `database` file is a list of entries, where each entry corresponds to one data sample. For example, take a look at an entry in a `database` of the `AFAD` dataset:

```
{
        "img_path": "AFAD/AFAD-Full/15/111/638660-1.jpg",
        "id_num": 638660,
        "age": 15,
        "gender": "M",
        "database": "AFAD-Full",
        "folder": 0,
        "aligned_bbox": [
            -57,
            -95,
            372,
            -83,
            360,
            346,
            -69,
            334
        ],
        "alignment_source": "in the wild"
    }
```

We can see that the entry defines multiple attributes. For us, the important attributes are:

- `img_path`: Path to the original, raw, image from the dataset.
- `age`: The ground-truth age of the sample, i.e., the label `y`.
- `folder`: Assignment of the sample to an _imaginary folder_. We use these folders to define training, validation and test parts. E.g., folders 0, 1 and 2 might correspond to the training part, which folder 3 corresponds to the validation part. These assignments are given to the sample during the creation of the `database` file. For each unique person, we always assign all of their samples to a single folder (subject-exclusive splitting) to avoid information leakage between training, validation and testing parts.
- `aligned_bbox`: For tasks such as age estimation, we do not want to use the entirety of the image as input to our model. Instead, we want to extract a part of the image corresponding to the face of the person. To this end, we locate the face and normalize its position by an aligning bounding box. This bounding box (or possibly, its extension) should be cropped out to be used as the data sample `x`.

## Data Splits

To separate data into training, validation and testing parts, we use the `folder` attribute of the entry in a `database` file.

For datasets that define a single training, validation and test split (CLAP2016 and CACD2000), we use the following data splits by `folder`.

```
  - trn: [0]
    val: [1]
    tst: [2]
```

For the remaining datasets, we define a total of 10 folders (0-9). See scripts in `facebase/to_json/` for more information.

We then define a total of 5 subject exlusive splits as follows:

### Split 0

```
  - trn: [0,1,2,3,4,5]
    val: [6,7]
    tst: [8,9]
```

### Split 1

```
  - trn: [2,3,4,5,6,7]
    val: [8,9]
    tst: [0,1]
```

### Split 2

```
  - trn: [4,5,6,7,8,9]
    val: [0,1]
    tst: [2,3]
```

### Split 3

```
  - trn: [5,6,7,8,9,0]
    val: [1,2]
    tst: [3,4]
```

### Split 4

```
  - trn: [6,7,8,9,0,1]
    val: [2,3]
    tst: [4,5]
```

# JSON origin

| file                           | origin                                                                                                    |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| AFAD-Full.json                 | created by `facebase/to_json/afad_to_json.py`                                                             |
| AgeDB.json                     | created by `facebase/to_json/agedb_to_json`                                                               |
| CACD2000.json                  | created by `facebase/to_json/cacd_to_json.py`                                                             |
| CLAP2016.json                  | created by `facebase/to_json/clap2016_to_json.py`                                                         |
| FG-Net.json                    | created by `facebase/to_json/fgnet_to_json.py`                                                            |
| IMDB-CLEAN-FP-AGE.json         | created by `facebase/to_json/imdb_fp_age_to_json.py`                                                      |
| IMDB-EM-CNN.json               | created by `facebase/to_json/imdb_to_json.py`                                                             |
| MORPH.json                     | created by `facebase/to_json/morph_to_json.py`                                                            |
| UTKFace.json                   | created by `facebase/to_json/utkface_to_json.py`                                                          |
| AFAD-Full_aligned.json         | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/AFAD-Full.json`         |
| AgeDB_aligned.json             | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/AgeDB.json`             |
| CACD2000_aligned.json          | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/CACD2000.json`          |
| CLAP2016_aligned.json          | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/CLAP2016.json`          |
| FG-Net_aligned.json            | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/FG-Net.json`            |
| IMDB-CLEAN-FP-AGE_aligned.json | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/IMDB-CLEAN-FP-AGE.json` |
| IMDB-EM-CNN_aligned.json       | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/IMDB-EM-CNN.json`       |
| MORPH_aligned.json             | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/MORPH.json`             |
| UTKFace_aligned.json           | created by `prepare_alignment alignment_config.yaml facebase/benchmarks/databases/UTKFace.json`           |

# Conventions used in the database JSON files

## Age

The attribute `age` denotes the number of completed years (revolutions of the subject around the sun). I.e. the values are `0, 1, 2, ..., max_age`. A special value `-1` means unknown age.

## Gender

The attribute `gender` denotes the biological gender as defined by the source datasets: `'M'` stand for male; `'F'` stands for female; `'U'` means unknown gender.

## License
The code used to create the database JSONs is released under the MIT License. The annotations themselves adhere to the original licenses of the dataset creators.
