# Image Quality Assessment


This repository provides an implementation of an aesthetic and technical image quality model based on Google's research paper ["NIMA: Neural Image Assessment"](https://arxiv.org/pdf/1709.05424.pdf). 
NIMA consists of two models that aim to predict the aesthetic and technical quality of images, respectively. The models are trained via transfer learning, where ImageNet pre-trained CNNs are used and fine-tuned for the classification task.



## Getting started

1. Install [jq](https://stedolan.github.io/jq/download/)

2. Install [Docker](https://docs.docker.com/install/)

3. Build docker image `docker build -t nima-cpu . -f Dockerfile.cpu`

    ```
## Train locally on CPU

1. Download dataset (see instructions under [Datasets](#datasets))

1.1 The "ava_labels_train.json" file only contains imaged id and labels for image category : "interior"

2. Run the local training script (e.g. for AVA dataset)
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

1. Single image file
    ```bash
    ./predict  \
    --docker-image nima-cpu \
    --base-model-name MobileNet \
    --weights-file $(pwd)/models/MobileNet/weights_mobilenet_technical_0.11.hdf5 \
    --image-source $(pwd)/src/tests/test_images/42039.jpg
    ```

2. All image files in a directory
    ```bash
    ./predict  \
    --docker-image nima-cpu \
    --base-model-name MobileNet \
    --weights-file $(pwd)/models/MobileNet/weights_mobilenet_technical_0.11.hdf5 \
    --image-source $(pwd)/src/tests/test_images

## Contribute
We welcome all kinds of contributions and will publish the performances from new models in the performance table under [Trained models](#trained-models).

For example, to train a new aesthetic NIMA model based on InceptionV3 ImageNet weights, you just have to change the `base_model_name` parameter in the config file `models/MobileNet/config_aesthetic_gpu.json` to "InceptionV3". You can also control all major hyperparameters in the config file, like learning rate, batch size, or dropout rate.

See the [Contribution](CONTRIBUTING.md) guide for more details.

## Datasets
This project uses two datasets to train the NIMA model:

1. [**AVA**](https://github.com/ylogx/aesthetics/tree/master/data/ava) used for aesthetic ratings ([data](http://academictorrents.com/details/71631f83b11d3d79d8f84efe0a7e12f0ac001460))
2. [**TID2013**](http://www.ponomarenko.info/tid2013.htm) used for technical ratings

For training on AWS EC2 we recommend to build a custom AMI with the AVA images stored on it. This has proven much more viable than copying the entire dataset from S3 to the instance for each training job.


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

For the AVA dataset these frequency distributions are given in the raw data files. For the TID2013 dataset we inferred the normalized frequency distribution, i.e. probability distribution, by finding the maximum entropy distribution that satisfies the mean score. The code to generate the TID2013 labels can be found under `data/TID2013/get_labels.py`.

For both datasets we provide train and test set label files stored under
```
data/AVA/ava_labels_train.json
data/AVA/ava_labels_test.json
```
and
```
data/TID2013/tid2013_labels_train.json
data/TID2013/tid2013_labels_test.json
```

For the AVA dataset we randomly assigned 90% of samples to the train set, and 10% to the test set, and throughout training a 5% validation set will be split from the training set to evaluate the training performance after each epoch. For the TID2013 dataset we split the train/test sets by reference images, to ensure that no reference image, and any of its distortions, enters both the train and test set.

## Serving NIMA with TensorFlow Serving
TensorFlow versions of both the technical and aesthetic MobileNet models are provided,
along with the script to generate them from the original Keras files, under the `contrib/tf_serving` directory.

There is also an already configured TFS `Dockerfile` that you can use.

To get predictions from the aesthetic or technical model:
1. Build the NIMA TFS Docker image `docker build -t tfs_nima contrib/tf_serving`
2. Run a NIMA TFS container with `docker run -d --name tfs_nima -p 8500:8500 tfs_nima`
3. Install python dependencies to run TF serving sample client
    ```
    virtualenv -p python3 contrib/tf_serving/venv_tfs_nima
    source contrib/tf_serving/venv_tfs_nima/bin/activate
    pip install -r contrib/tf_serving/requirements.txt
    ```
4. Get predictions from aesthetic or technical model by running the sample client
    ```
    python -m contrib.tf_serving.tfs_sample_client --image-path src/tests/test_images/42039.jpg --model-name mobilenet_aesthetic
    python -m contrib.tf_serving.tfs_sample_client --image-path src/tests/test_images/42039.jpg --model-name mobilenet_technical
    ```



## Copyright

See [LICENSE](LICENSE) for details.
