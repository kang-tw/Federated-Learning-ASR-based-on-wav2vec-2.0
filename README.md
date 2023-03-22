# Federated-Learning-ASR-based-on-wav2vec-2.0

This works presents the source code using [Federated Learning (FL) in ASR domain based on Wav2Vec 2.0 model](https://arxiv.org/pdf/2302.10790.pdf) [1]. This approach has been proved that can overcome non-IID problem which usually oberserve in FL scenario thanks to the power of Self-supervised learning (SSL) in general or Wav2Vec 2.0 in particular. An experiment was conducted on [TED-LIUM 3 ](https://lium.univ-lemans.fr/en/ted-lium3/) dataset and provide a promissing ASR performance with word error rate of 10.92%, without sharing any data from the different users. We also anaylyzed the ASR performance at speaker level and it's ability to protect speaker identity since the target of Federated Learning is to ensure the privacy. 

The repository itself combines [Flower](https://flower.dev) and [Speechbrain](https://speechbrain.github.io) to achieve training ASR models in FL setting. It was an extension forward from [Flower-SpeechBrain](https://github.com/yan-gao-GY/Flower-SpeechBrain) repository which apply directly the used of Wav2Vec 2.0 and simulate the FL on the same machine. 


> *[1]Tuan Nguyen, Salima Mdhaffar, Natalia Tomashenko, Jean-François Bonastre, and Yannick Estève. 2023. Federated Learning for ASR based on Wav2vec 2.0 in ICASSP 2023*

Detailed descriptions of experiments and results are given in on our paper here : [link](https://arxiv.org/abs/2302.10790)


## Acknowledgements

We would love to express our gratitude toFrench National Research Agency (ANR) since the project was funded by them in the context of project DEEP-PRIVACY. 
Also, we would love to express our sincere thanks to [Laboratoire Informatique d'Avignon](https://liavignon.fr) and Avignon Université for providing us with all necessary facilities to conduct the research.

## Requirements

* Flower <=0.18.0, >= 0.17.0

* Speechbrain

* Pytorch >= 1.5.0

* Python >=3.7.0

* Ray-proxy

* Torchaudio

* Pandas

*numpy <= 1.23.0

* GPUs: mem >= 24Gb

## Data Structure
Below is strucutre recommended to adapt the code. A data folder contains all processed csv files is provided for community to re-produce the result of paper. All audio files could be find at [TED-LIUM 3 website](https://projets-lium.univ-lemans.fr/ted-lium/release3/)  (Please change the path in csv file to your actual audio path)

You can also run the provided script *data_prepare.py* to download audio and change the csv path

```bash
├── data (provided)
│   ├── client_{cid}
│   │   ├── ted_train.csv
│   │   ├── ted_dev.csv
│   │   ├── ted_test.csv
│   │   ├── ted_train_full5.csv {Analysis dataset contains only 5m from ted_train.csv}
│   │   ├── ted_train_wo5.csv {ted_train.csv - ted_train_full5.csv, the training in our paper}
│   ├── ted_train.csv {all TED-LIUM 3 train set}
│   ├── ted_dev.csv {all TED-LIUM 3 valid set}
│   ├── ted_test.csv {all TED-LIUM 3 test set}
├── output
│   ├── client_{cid}
│   │   ├── log.txt
│   │   ├── label_encoder.txt {if dont use the same label for each client and server, file will be generate for each client by Speechbrain}
│   │   ├── ....
│   ├── client_19999 {server - number 19999 just to distinguish with client cid. You can choose any number in the py file.}
│   │   ├── log.txt
│   │   ├── train_log.txt {where you can find performance per round}
│   │   ├── label_encoder.txt {if dont use the same label for each client and server}
│   │   ├── ....
├── material
│   ├── label_encoder.txt {If use the same label encoder for all - provided}
│   ├── pre-trained
│   │   ├── model.ckpt
│   ├── wav2vec checkpoint {Highly recommend to have own huggingface wav2vec2 checkpoint to avoid repeat download wav2vec2}
│   │   ├── ....

```

## Running flow
Due to the size of Wav2Vec 2.0 model and dataset, highly recommend to enavle device as cpu so clients will be initialize and store at CPUs memory and only use GPUs memory when needed. 

## How to run

Simply run this command below (example):
```bash
python fl_w2v.py \
  --data_path="./data" \
  --config_path="./configs/w2v2.yaml" \
  --min_fit_clients=2 \
  --running_type="cpu" \
  --min_available_clients=2 \
  --output="./output/" \
  --fraction_fit=0.01 \
  --rounds=2 \
  --parallel_backend=True \
  --pre_train_model_path="..../material/model.ckpt" \
  --label_path="/users/tnguyen/Federated_learning/results/mcadam/1234/save/label_encoder.txt" \
  --local_epochs=1

where: 
 - data_path: path to data folder
 - config_path: path to yaml file
 - min_fit_clients: the minium number of clients to involve in the training per round
 - fraction_fit : ratio number of client over total clients involve in the training per round
 - svae_path_pre: output folder
 - rounds: Number of global round for FL
 - parallel_backend (default = True): If assign multiple GPUs per client
 - pre_train_model_path (optional): path to pre-trained starting point (in case of resume training or having pre-trained on ASR task as starting point)
 - label_path (optional):  path to label encoder files if ensure having same encoder for all
 - local_epochs : Number of local epoch for local/client side
 - device (default = "cpu" - recommended): If "cpu", all clients and server are initialized on CPU and only pump to GPUs if needed in order to ensure enough GPUs memmory. If dataset is small or on small scale, could switch to "cuda" which will be faster (to avoid switing GPU and CPU time).
```
## Citation
Please citation for your upcoming works if used
```bash
@misc{https://doi.org/10.48550/arxiv.2302.10790,
doi = {10.48550/ARXIV.2302.10790},url = {https://arxiv.org/abs/2302.10790}, 
author = {Nguyen, Tuan and Mdhaffar, Salima and Tomashenko, Natalia and Bonastre, Jean-François and Estève, Yannick}, 
keywords = {Audio and Speech Processing (eess.AS), Machine Learning (cs.LG), Sound (cs.SD), FOS: Electrical engineering, electronic engineering, information engineering, FOS: Electrical engineering, electronic engineering, information engineering, FOS: Computer and information sciences, FOS: Computer and information sciences},
title = {Federated Learning for ASR based on Wav2vec 2.0}, 
publisher = {arXiv},year = {2023},
copyright = {Creative Commons Attribution 4.0 International}}

```
