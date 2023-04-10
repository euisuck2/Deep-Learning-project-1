[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
# Image Quality Assessment


This repository provides an implementation of an aesthetic and technical image quality model based on Google's research paper ["NIMA: Neural Image Assessment"](https://arxiv.org/pdf/1709.05424.pdf). 
NIMA consists of two models that aim to predict the aesthetic and technical quality of images, respectively. The models are trained via transfer learning, where ImageNet pre-trained CNNs are used and fine-tuned for the classification task.



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
## Contribute
We welcome all kinds of contributions and will publish the performances from new models in the performance table under [Trained models](#trained-models).

For example, to train a new aesthetic NIMA model based on InceptionV3 ImageNet weights, you just have to change the `base_model_name` parameter in the config file `models/MobileNet/config_aesthetic_gpu.json` to "InceptionV3". You can also control all major hyperparameters in the config file, like learning rate, batch size, or dropout rate.

See the [Contribution](CONTRIBUTING.md) guide for more details.

## Datasets
This project uses two datasets to train the NIMA model:

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
data/AVA/ava_labels_test.json
```


For the AVA dataset we randomly assigned 90% of samples to the train set, and 10% to the test set, and throughout training a 5% validation set will be split from the training set to evaluate the training performance after each epoch. 


## Copyright

See [LICENSE](LICENSE) for details.
