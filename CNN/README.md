[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
# Aesthetic score assessment on Airbnb listing pictures


This repository provides an implementation of an aesthetic and technical image quality model based on Google's research paper ["NIMA: Neural Image Assessment"](https://arxiv.org/pdf/1709.05424.pdf) and idealo's technincal blog/github ["Image Quality Assessment"](https://devblogs.nvidia.com/deep-learning-hotel-aesthetics-photos/).
NIMA consists of two models that aim to predict the aesthetic and technical quality of images, respectively. The models are trained via transfer learning, where ImageNet pre-trained CNNs are used and fine-tuned for the classification task.
For this task, our team only measured aesthetic quality of images using MobileNet and InceptionV3. The serving used in this task was built upon idealo's code (https://github.com/idealo/image-quality-assessment) and modified to fit our task. This repository provides weights and configs for the model our team trained using only images belong to 'interior' category from AVA dataset.

## Methodology

<img width="1122" alt="Screen Shot 2023-04-07 at 4 27 42 PM" src="https://user-images.githubusercontent.com/5760152/231254166-59a3c982-7c99-4758-a8cf-b9106d63ba97.png">

For the final aesthetic score, our team used predictions from two different Imagenet-based CNN models and averaged them. 
At first, scores were calculated using three different models : MobileNet trained using the entire AVA dataset, MobileNet trained using only "interior" dataset, and InceptionV3 trained using only "interior" dataset. The MobilNet trained only on "interior" dataset performed poorly so our team dropped the score and compared the rest of the predictions from the models.

For MobileNet, we used the model with ImageNet weights and replaced the last dense layer in MobileNet with a dense layer that outputs scores from 1 to 10. For InceptionV3, we trained the model from scratch and fine-tuned the model to produce the output scores. Then, we averaged those scores for each image from Airbnb listing and applied MinMax normalization since the scores were too cluttered to the mean.


## Trained Model

<img width="868" alt="trained model" src="https://user-images.githubusercontent.com/5760152/231256573-2d0d95a7-e8b4-4e57-89f7-381c6b5aa7b8.png">
The trained model performed as shown above. MobileNet(Entire data) and InceptionV3(Interior data) performs quite well, which somewhat aligns with human judgement. 

## Getting started

1. Install [jq](https://stedolan.github.io/jq/download/)

2. Install [Docker](https://docs.docker.com/install/)

3. Build docker image `docker build -t nima-cpu . -f Dockerfile.cpu`

## Train locally on CPU

1. Download dataset (see instructions under [Datasets](#datasets))

2. The "ava_labels_train.json" file only contains imaged id and labels for image category : "interior"

3. Run the local training script (e.g. for AVA dataset)
    ```bash
    ./train-local \
    --config-file $(pwd)/models/MobileNet/config_technical_cpu.json \
    --samples-file $(pwd)/data/AVA/ava_labels_train.json \
    --image-dir /path/to/image/dir/local
    ```
This will start a training container from the Docker image `nima-cpu` and create a timestamp train job folder under `train_jobs`, where the trained model weights and logs will be stored. The `--image-dir` argument requires the path of the image directory on your local machine.

In order to stop the last launched container run
    ```bash
    CONTAINER_ID=$(docker ps -l -q)
    docker container stop $CONTAINER_ID
    ```

In order to stream logs from last launched container run
    ```bash
    CONTAINER_ID=$(docker ps -l -q)
    docker logs $CONTAINER_ID --follow
    ```

## Predict
In order to run predictions on an image or batch of images you can run the prediction script

All image files in a directory
    ```bash
    ./predict \
    --config-file $(pwd)/models/MobileNet/config_technical_cpu.json \
    --samples-file $(pwd)/data/AVA/ava_labels_train.json \
    --image-source $(pwd)/src/tests/test_images
    ```

## Datasets
This project uses a publicaly accessible dataset to train the NIMA model:

1. [**AVA**](https://github.com/ylogx/aesthetics/tree/master/data/ava) used for aesthetic ratings ([data](http://academictorrents.com/details/71631f83b11d3d79d8f84efe0a7e12f0ac001460))


## Label files
The **train** script requires JSON label files in the format
```
[
  {
    "image_id": "231893",
    "label": [2,8,19,36,76,52,16,9,3,2]
  },
  {
    "image_id": "746672",
    "label": [1,2,7,20,38,52,20,11,1,3]
  },
  ...
]
```

The label for each image is the normalized or un-normalized frequency distribution of ratings 1-10.

For the AVA dataset these frequency distributions are given in the raw data files.

Train and test set label files stored under
```
data/AVA/ava_labels_train.json
data/AVA/ava_labels_train_entire.json
data/AVA/ava_labels_test.json
```


For the AVA dataset we randomly assigned 90% of samples to the train set, and 10% to the test set, and throughout training a 5% validation set will be split from the training set to evaluate the training performance after each epoch. 
There are two train json files: a train set that only contains "interior" category images and entire image labels. 


## Copyright

See [LICENSE](LICENSE) for details.
