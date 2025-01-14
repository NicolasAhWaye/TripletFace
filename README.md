Triplet loss for facial recognition.

# Triplet Face

The repository contains code for the application of triplet loss training to the
task of facial recognition. This code has been produced for a lecture and is not
going to be maintained in any sort.

![TSNE_Latent](TSNE_Latent.png)

## Architecture

The proposed architecture is pretty simple and does not implement state of the
art performances. The chosen architecture is a fine tuning example of the
resnet18 CNN model. The model includes the freezed CNN part of resnet, and its
FC part has been replaced to be trained to output latent variables for the
facial image input.

The dataset needs to be formatted in the following form:
```
dataset/
| test/
| | 0/
| | | 00563.png
| | | 01567.png
| | | ...
| | 1/
| | | 00011.png
| | | 00153.png
| | | ...
| | ...
| train/
| | 0/
| | | 00001.png
| | | 00002.png
| | | ...
| | 1/
| | | 00001.png
| | | 00002.png
| | | ...
| | ...
| labels.csv        # id;label
```

## Install

Install all dependencies ( pip command may need sudo ):
```bash
cd TripletFace/
pip3 install -r requirements.txt
```

## Usage

For training:
```bash
usage: train.py [-h] -s DATASET_PATH -m MODEL_PATH [-i INPUT_SIZE]
                [-z LATENT_SIZE] [-b BATCH_SIZE] [-e EPOCHS]
                [-l LEARNING_RATE] [-w N_WORKERS] [-r N_SAMPLES]

optional arguments:
  -h, --help            show this help message and exit
  -s DATASET_PATH, --dataset_path DATASET_PATH
  -m MODEL_PATH, --model_path MODEL_PATH
  -i INPUT_SIZE, --input_size INPUT_SIZE
  -z LATENT_SIZE, --latent_size LATENT_SIZE
  -b BATCH_SIZE, --batch_size BATCH_SIZE
  -e EPOCHS, --epochs EPOCHS
  -l LEARNING_RATE, --learning_rate LEARNING_RATE
  -w N_WORKERS, --n_workers N_WORKERS
  -r N_SAMPLES, --n_samples N_SAMPLES
```

## References

* Resnet Paper: [Arxiv](https://arxiv.org/pdf/1512.03385.pdf)
* Triplet Loss Paper: [Arxiv](https://arxiv.org/pdf/1503.03832.pdf)
* TripletTorch Helper Module: [Github](https://github.com/TowardHumanizedInteraction/TripletTorch)

## Todo ( For the students )

**Deadline Decembre 13th 2019 at 12pm**

The students are asked to complete the following tasks:
* Fork the Project
* Improve the model by playing with Hyperparameters and by changing the Architecture ( may not use resnet )
* JIT compile the model ( see [Documentation](https://pytorch.org/docs/stable/jit.html#torch.jit.trace) )
* Add script to generate Centroids and Thesholds using few face images from one person
* Generate those for each of the student included in the dataset
* Add inference script in order to use the final model
* Change README.md in order to include the student choices explained and a table containing the Centroids and Thesholds for each student of the dataset with a vizualisation ( See the one above )
* Send the github link by mail

## Résultats
Amélioration du model: nombre epoch=3, Utilisation de resnet152 à la place de resnet 18

![Visualization](vizualisation.png)

Code pour le JIT:

```
import torch
import torch.nn as nn
from TripletFace.tripletface.core.model import Encoder
import torch

model = Encoder(64)
weights = torch.load("/content/drive/My Drive/model.pt")['model']
model.load_state_dict( weights )
jit_model = torch.jit.trace( model, torch.rand(8, 3, 3, 3) )
torch.jit.save( jit_model, "scriptModel.pt" )
```
Accès Dataset

```
from google.colab import drive
drive.mount('/content/drive')
```
Lancement du train
```
!cd TripletFace && python3 -m tripletface.train -s '/content/drive/My Drive/dataset' -m '/content/TripletFace'
```

Centroïdes et Tresholds: je suis désolé, je n'ai pas réussis à calculer cela. Cependant, je peux vous donner l'algorithme permettant de calculer ceci
```
Calcul Centroïdes
1.begin
2. for each level k, 1<=k<=nombre image do
3. 	for each node n at level k, 1<=n<=4^k-1 do
4.		for each bin i of data at node n, 1<=i<=B do
5.			Find (min,max) of image over all images in the data
6.			Centroids = (min+max)/2
7.		endfor
8.	endfor
9.endfor
10.end
Calcul Thresholds
1.begin
2.	for each leval k, 1<= k<=nombre image do
3.		for each image appartenant au dataset, 1<=i<=nombre image dataset do
4.		di= distance entre chaque centroïds
5.		endfor
6.	Thresholds=Max(di)
7.	enfor
8.end 

```

