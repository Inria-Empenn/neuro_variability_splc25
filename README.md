# In the Search for Truth: Navigating Variability in Neuroimaging Software Pipelines
Youenn Merel-Jourdan, Mathieu Acher, Camille Maumet

Submitted to SPLC'25, September 01-September 05, 2025 Coruña, Spain

[![SWH](https://archive.softwareheritage.org/badge/swh:1:dir:b4c8f777c77a70d98351461efb07e50d18641790/)](https://archive.softwareheritage.org/swh:1:dir:b4c8f777c77a70d98351461efb07e50d18641790;origin=https://github.com/Inria-Empenn/neuro_variability_splc25;visit=swh:1:snp:e6cb0b387b560f4324237664bcc0f0c9b458e300;anchor=swh:1:rev:ac9c7052cae29072782530874064b1c4a1f03996)

# Data

Data used in the paper is available in the `data` directory

## UVL feature model

The UVL feature model expressing the explored analytical space is at `data/model/full_pipeline.uvl` 

## fMRI data

Task-fMRI dataset is available in `data/auditory`. It was downloaded from https://www.fil.ion.ucl.ac.uk/spm/data/auditory/

## Configurations

The configurations used in the experiment are available in the `data/configs`

The 1000 randomly sampled configuration are divided between 20 CSV files (50 rows + header) named `config_[n].csv`

The expert-crafted configuration is `config_ref.csv`.

## Data descriptor

`data/auditory/data_desc_auditory.json` is a JSON file describing the experiments data and some execution paths.

# Sampling

The code used for this part is available at https://github.com/Inria-Empenn/fmri_feature_model [![SWH](https://archive.softwareheritage.org/badge/swh:1:dir:763e704299464012a925bee26659b001b1e54211/)](https://archive.softwareheritage.org/swh:1:dir:763e704299464012a925bee26659b001b1e54211;origin=https://github.com/Inria-Empenn/fmri_feature_model;visit=swh:1:snp:80f048cbc30d119646b894d9cd9f2e4401427fdd;anchor=swh:1:rev:473edb27cf19d6adfc40a4adb751eb7c17abad76)

## Pull & install project
``` shell
git clone https://github.com/Inria-Empenn/fmri_feature_model.git
cd fmri_feature_model
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Sample
Randomly sample 1000 configurations divided into 20 files (+ reference configuration)
``` sh
python sample.py --nconfig 1000 --parts 20
```

# Pipelines execution & postprocessing

The code used for this part is available at https://github.com/Inria-Empenn/fmri-conf-runner [![SWH](https://archive.softwareheritage.org/badge/swh:1:dir:418f69eb194e6df377aea957329401855fe210b4/)](https://archive.softwareheritage.org/swh:1:dir:418f69eb194e6df377aea957329401855fe210b4;origin=https://github.com/Inria-Empenn/fmri-conf-runner;visit=swh:1:snp:0f907b083a2abe4c90c6a18bb6443625ebd43a48;anchor=swh:1:rev:65f5cd990722478558a602884fed972600289078)

## Pull & install project
``` shell
git clone https://github.com/Inria-Empenn/fmri-conf-runner.git
cd fmri-conf-runner
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Build docker image
``` shell
docker build . -t fmri-conf-runner
```

Final image size is approximately 6.5 GB

## Pipelines execution

Change `/local/path/to/...` to your local paths

- `/local/path/to/data` : Will be mapped to `/data` in the container. This folder must contains
   - the `auditory` dataset/subfolder
   - `data_desc.json` file
- `/local/path/to/results` : This folder must exists. Will be mapped to `/results` in the container.
- `/local/path/to/workdir` : This folder must exists. Will be mapped to `/workdir` in the container.
- `/local/path/to/configs` : This folder must contains configuration CSV files (in this example `config.csv` and `config_ref.csv`). Will be mapped to `/configs` in the container.

``` sh
docker run -u root -v "/local/path/to/data:/data" -v "/local/path/to/results:/results" -v "/local/path/to/workdir:/work" -v "/local/path/to/configs:/configs" fmri-conf-runner python -u run.py --configs "/configs/config.csv" --data /data/data_desc.json --ref /configs/config_ref.csv
```

On Abaca (Inria cluster), use `run_configs.sh`
```sh
oarsub -S -n fmri-conf-runner ./run_configs.sh
```

## Postprocessing

Change `/local/path/to/...` to your local paths
``` sh
docker run -u root -v "/local/path/to/results:/results" fmri-conf-runner python -u postprocess.py --results "/results"
```

On Abaca (Inria cluster), use `postprocess.sh`
```sh
oarsub -S -n postprocess ./postprocess.sh
```

# Analysis

Analysis code and results can be found in `analysis`

`analysis/auditory_analysis.ipynb` is the Jupyter notebook used to process and analyse the data.

`analysis/classifier` contains the output from classifier decision tree training (dot and pdf files)

`analysis/regression` contains the output from regression decision tree training (dot and pdf files)



