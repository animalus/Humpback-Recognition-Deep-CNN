# Humpback Recognition using deep CNNs
This project uses deep convolutional neural networks to predict whale identities from images of their flukes. Currently, the model requires images to be precropped.

## Docker Instructions
    
    docker-machine start humpbackModel
    eval "$(docker-machine env humpbackModel)"
    
For ease of use, this repo includes a dockerfile at Docker/Dockerfile that can be used to run the model.

If you'd like to run the model in CPU-only mode, change the first line in the Dockerfile to
```
FROM gcr.io/tensorflow/tensorflow
```

Then, you'll need to build the docker image (installing dependencies etc may take a little while):
```
docker build -t humpbackmodel Docker
```

Once the docker image is built, you can run it and mount this github repo inside of it:
```
docker run -it -v /full/path/to/this/repo/:/hrdcnn-repo/ -v /full/path/to/data:/hrdcnn-data humpbackmodel
```

Then, you should be able to run the model inside the container:
```
python /humpbackModel/run.py -h
```

## Usage and examples
Usage of run.py can also be obtained by ```python run.py -h```

### Prediction
To predict the identity of a whale in an image, you can run:
```
python run.py --trainCSV trainingData.csv --imageDir imageDirectory/ --predict whale-fluke-image.jpg --storedWeights weightsFile.h5
```

### Evaluation
To guage the accuracy of the model on the held out test set:
```
python run.py --trainCSV trainingData.csv --imageDir imageDirectory/ --evaluate --storedWeights weightsFile.h5
```

### Training
To train a model from scratch:
```
python run.py --trainCSV trainingData.csv --imageDir imageDirectory/ --train
```

## Additional Files
The default model in this project uses the first several layers of VGGNet for feature extraction. The script expects a saved keras VGGNet model file to be located at VGG/FullVGGWeights.h5. It's not included in this repository as the weights are over 500mb. Contact me if you need them - I'll host them publically at some point soon.

## Generating training data
To generate trainingData.csv from a new batch of images, you can use the preprocessing/humpbackData.py script:
```
python preprocessing/humpbackData.py <zooniverse file> <image directory>
```

Note that this new dataset will contain a different number of whales (and in a different order) than you  had when training previous models, so their stored weights will not be compatible with the new data set.

This will be resolved soon, and only the final fully connected layer will need to be retrained when given new whales to identify.

#Ken's Section For Safe Keeping for the moment
##Building Docker

    cd /Users/ken/dev/animalus/idservices/Humpback-Recognition-Deep-CNN
    docker build -t hrdcnn Docker

##Running Docker

    docker run -it -v /Users/ken/dev/animalus/idservices/Humpback-Recognition-Deep-CNN:/hrdcnn-repo -v /Users/ken/dev/animalus/idservices/HRD-CNN-data:/hrdcnn-data hrdcnn

##Predicting:

From within /hrdcnn-data run ...

    python /hrdcnn-repo/run.py --trainCSV /hrdcnn-data/trainingData.csv --storedWeights /hrdcnn-data/currentModel.h5 --imageDir /hrdcnn-data/orig25 --predict orig25/15243-20101013-jac-0224.jpg

##Installing docker on dev.happywhale.com to use another aws instance for the docker.

    sudo bash
    apt-get update
    apt-get install apt-transport-https ca-certificates
    apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
    
    nano /etc/apt/sources.list.d/docker.list
    
If ubuntu Trusty 14.04 add the line or the equivalent for your version...
    
    deb https://apt.dockerproject.org/repo ubuntu-trusty main
    
Then continue running ...

    apt-get update
    apt-get purge lxc-docker
    apt-cache policy docker-engine
    apt-get install linux-image-extra-$(uname -r)
    apt-get install docker-engine
    service docker start

Verify docker installed correctly...

    sudo docker run hello-world
    
    