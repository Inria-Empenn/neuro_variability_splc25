# In the Search for Truth: Navigating Variability in Neuroimaging Software Pipelines
Youenn Merel-Jourdan, Mathieu Acher, Camille Maumet

Submitted to SPLC'25, September 01-September 05, 2025 Coruña, Spain

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

`data/auditory/data_desc_auditory.json` is a JSON file describing the experiments data

# Sampling

The code used for this part is available at https://github.com/Inria-Empenn/fmri_feature_model

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

The code used for this part is available at https://github.com/Inria-Empenn/fmri-conf-runner

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

## Pipelines execution

Change `/local/path/to/...` to your local paths
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



