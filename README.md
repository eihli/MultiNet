<p align="center">
  <img src="assets/Multinetlogo.png" alt="Multinet Logo" height="100" style="vertical-align: middle;">
  <h1 align="center" style="display: inline-block; vertical-align: middle; margin-left: 20px;">MultiNet: A Generalist Benchmark for Vision-Language & Action models</h1>
</p>

<p align="center">
  <a href="https://multinet.ai">Website</a> •
  <a href="https://multinet.ai/static/pdfs/Benchmarking%20Vision%20Language%20Action%20Models.pdf">Paper</a> •
  <a href="https://multinet.ai/static/pdfs/MultiNet_Dataset_Spec_Paper.pdf">Dataset Spec</a> •
  <a href="https://github.com/ManifoldRG/MultiNet/tree/main/src/modules">GenESIS framework</a> •
  <a href="https://discord.gg/Rk4gAq5aYr">Contribute</a>
</p>

### This work is sponsored by, and is being done in close collaboration with [Metarch](https://metarch.ai/).

## 📢 Updates
2024-11-08: We release the first version of MultiNet where we profiled a SoTA VLM, SoTA VLA, and a novel Generalist model on OpenX Embodiment datasets - Multinet v0.1! Check our [website](https://multinet.ai) for more details.


## 🔍 Overview

This repo provides the following capabilities:
1. Download any or all datasets of what aims to be the largest consolidation of open-source vision-language + control/action (RL, Robotics) data
2. Translate control data of various formats and from various sources to a unified [Tensorflow Dataset format](https://www.tensorflow.org/datasets/api_docs/python/tfds). 
3. Evaluate the performance of GPT-4o, OpenVLA, and HuggingFace's JAT in a zero-shot setting on OpenX Embodiment datasets using the benchmark released in [Multinet v0.1](https://github.com/ManifoldRG/MultiNet/releases/tag/v0.1).
4. A [general framework](https://github.com/ManifoldRG/MultiNet/tree/main/src/modules) for mapping VLMs to other modality classes, with particular emphasis on action spaces. This framework allows one to adapt a wide range of models to multiple types of tasks or datasets for scaling effectively while reducing the amount of engineering effort required.  In MultiNet v0.1, GenESIS is used to evaluate GPT-4-o on the OpenX datasets.

As a part of Multinet v0.1, we also release [μGATO](https://github.com/eihli/mugato) - a small, simple, open-source implementation of what is described in DeepMind's GATO paper. This is our first step towards building a multimodal generalist action model.

<p align="center">
  <img src="assets/Multinet v0.1 release visual 3.0.png" alt="Multinet Figure" style="vertical-align: middle;">
</p>

## 🚀 Getting Started

#### To set up the environment for download, translation, and evaluation of GPT-4o and HuggingFace's JAT:

```bash
conda create -n multinet python=3.10
conda activate multinet
git clone https://github.com/ManifoldRG/MultiNet.git
cd MultiNet/src
pip install -r requirements.txt
```

#### To download the datasets in v0:

```bash
cd Multinet/src
python centralized_downloader --dataset_name <name of dataset you would like to download> --output_dir <directory where you would like to download the dataset>
```

#### To translate one file/shard of your desired control dataset (downloaded using the downloader script in this repo) to the TFDS format 

```bash
cd Multinet/src/control_translation
python centralized_translation --dataset_name <name of dataset whose file you would like to translate> --dataset_path <path to the downloaded dataset> --output_dir <directory where you would like to store the translated file>
```

#### To translate multiple files/shards of your desired control dataset (downloaded using the downloader script in this repo) to the TFDS format 

Make sure to modify the way the multiple files are being traversed for translation in translate_multiple.py in Multinet/src/control_translation according to your local file structure.

```bash
cd Multinet/src/control_translation
python wrapper_translate_multiple.py --dataset_name <name of dataset whose file you would like to translate> --dataset_path <path to the downloaded dataset> --output_dir <directory where you would like to store the translated files>
```

#### To evaluate JAT (in a zero-shot setting) on the 53 OpenX Embodiment datasets it was profiled on as a part of Multinet v0.1 

Make sure to set the path to the translated openx datasets you want to evaluate on and the path where you want to dump your results in Multinet/src/eval/profiling/jat/scripts/profile_openx.py

```bash
cd Multinet/src/eval/profiling/jat/scripts
python profile_openx.py
```

#### To evaluate GPT-4o (in a zero-shot setting) using the [GenESIS framework](https://github.com/ManifoldRG/MultiNet/tree/main/src/modules) on the 22 OpenX Embodiment datasets it was profiled on as a part of Multinet v0.1 

Make sure to adjust the path creation to the translated openx datasets you want to evaluate on, and the path where you want to dump your results in src/modules/dataset_modules/openx_module.py based on your local file structure 

```bash
cd Multinet/src/eval/eval_main.py
python eval_main.py --disk_root_dir <path to the translated openx datasets> --dataset_name openx --model gpt-4o-2024-05-13
```
Enter the OpenAI API key when prompted.

#### To evaluate OpenVLA (in a zero-shot setting) on the 20 OpenX Embodiment datasets it was profiled on as a part of Multinet v0.1 

We set up our conda environment and ran evaluations for OpenVLA on a GCP Instance with 1 L4 GPU, driver version 550.90.07, and CUDA version 12.4. For more details about the infrastructure used, refer to our [paper](https://multinet.ai/static/pdfs/Benchmarking%20Vision%20Language%20Action%20Models.pdf). If you are using our code out-of-the-box, we recommend using the same infrastructure.

For setup, create a new conda environment and run the OpenVLA environment setup bash script (this will download both the OpenVLA requirements as well as the broader MultiNet requirements):

```bash
cd Multinet/src
./openvla_multinet_setup.sh
```

To run evaluations:

```bash
cd Multinet
python src/eval/profiling/openvla/experiments/robot/openvla_openx_profiling.py --openx_datasets_path <path to the translated openx datasets> --dataset_statistics_path src/eval/profiling/openvla/data/dataset_statistics.json --result_save_path <path to the directory where you would like to save your results>
```
