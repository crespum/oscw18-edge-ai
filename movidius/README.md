# Intel Movidius and Raspberry Pi

This folder contains the files required to run Inception v(1-4) on the [Intel® Movidius™ Neural Compute Stick](https://www.movidius.com/) on a Raspberry Pi. To generate the files for inference, follow the steps in [Compiling the model](#compiling-the-model). For those of you who just want to run inference on the stick itself, some precompiled files have been uploaded to the `model` directory. If this is the case check the [Running inference](#running-inference) section.

## Compiling the model
*NOTE: Execute the following instructions on a an x86_64 machine running Ubuntu 16.04 (development machine).*
1. Follow the [official guide](https://movidius.github.io/ncsdk/install.html) to install **NCSDK v2** on the development machine.
2. Get the sources:
```
mkdir -p ~/workspace/tensorflow

# Clone TensorFlow source and models repo
cd ~/workspace/tensorflow
git clone https://github.com/tensorflow/tensorflow
git clone https://github.com/tensorflow/models

# Clone this repo
cd ~/workspace
git clone https://github.com/crespum/oscw18-edge-ai.git
```
3. Compile a model:
```
# Download, export, freeze and profile model
cd ~/workspace/oscw18-edge-ai/movidius/
export TF_SRC_PATH=~/workspace/tensorflow/tensorflow
export TF_MODELS_PATH=~/workspace/tensorflow/models
make VERSION=v1 compile
```

## Running inference
*NOTE: Execute the following instructions on a Raspberry Pi with an Intel Movidius Neural Compute Stick.*

1. Install NCSDK v2 API. This will not install the entired SDK but just the interface for using the stick.
```
# Install dependencies
sudo apt-get install -y libusb-1.0-0-dev libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler libatlas-base-dev git automake byacc lsb-release cmake libgflags-dev libgoogle-glog-dev liblmdb-dev swig3.0 graphviz libxslt-dev libxml2-dev gfortran python3-dev python-pip python3-pip python3-setuptools python3-markdown python3-pillow python3-yaml python3-pygraphviz python3-h5py python3-nose python3-lxml python3-matplotlib python3-numpy python3-protobuf python3-dateutil python3-skimage python3-scipy python3-six python3-networkx python3-tk

# Compile and install API
git clone https://github.com/movidius/ncsdk -b ncsdk2
cd ncsdk/api/src
make
sudo make install
```
2. Clone this repo
```
git clone https://github.com/crespum/oscw18-edge-ai.git
cd oscw18-edge-ai/movidius
make infer # See available parameters below
```
We can use two parameters:
* `VERSION` to select the Inception version to compile (v1-v4)
* `INPUT` to select on which we want to run inference. A sample image has been uploade to the repo too.
We could for instance use `make VERSION=v4 INPUT=foo.jpg`

The output should look like this:
```
$ make infer
Running inferences using image-classifier project...
(python3 image-classifier.py --graph ./model/v1/graph_ncgraph --dim 224 224 --mean 127.5 --scale 0.00789 --colormode "RGB" --labels ./model/v1/labels.txt --image data/pic_002.jpg)

==============================================================
Top predictions for pic_002.jpg
Execution time: 88.5288ms
--------------------------------------------------------------
74.7%	288:lynx, catamount
8.1%	283:tiger cat
4.8%	282:tabby, tabby cat
4.1%	284:Persian cat
1.2%	286:Egyptian cat
==============================================================
```

If you want to use the files compiled in the [previous section](#compiling-the-model) you need to copy these two files from the development machine to the Raspberry Pi (replace the name of the last folder accordingly):
```
cd ~/workspace/oscw18-edge-ai/movidius/
scp model/v1/labels.txt pi@raspberrypi.local:/home/pi/oscw18-edge-ai/movidius/model/v1/
scp model/v1/labels.txt pi@raspberrypi.local:/home/pi/oscw18-edge-ai/movidius/model/v1/
```
