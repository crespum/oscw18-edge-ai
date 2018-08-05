# TensorRT and NVIDIA Jetson TX1
This folder contains the files required to run Inception v(1-4) using [TensorRT](https://developer.nvidia.com/tensorrt) on the [NVIDIA Jetson TX1](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems-dev-kits-modules/). Altough it has not been tested on the new models like the TX2, they should be compatible.

# Setting up the environment

## Compiling the model
*NOTE: Execute the following instructions on the Jetson's command line. The instructions are heavily based on [this sample app](https://github.com/NVIDIA-Jetson/tf_to_trt_image_classification).*

1. Flash the Jetson using JetPack 3.2.  Be sure to install the following packages:
   * CUDA 9.0
   * OpenCV4Tegra
   * cuDNN
   * TensorRT 3.0
2. Install pip.
    ```
    sudo apt-get install python3-pip
    ```
3. Install TensorFlow:
   1. Download the TensorFlow 1.5.0 pip wheel:
        ```
        wget https://github.com/peterlee0127/tensorflow-nvJetson/releases/download/binary/tensorflow-1.5.0-cp35-cp35m-linux_aarch64.whl
        ```
   2. Install TensorFlow using `pip3`:
        ```
        sudo pip3 install 
        tensorflow-1.5.0-cp35-cp35m-linux_aarch64.whl
        ```
4. Install uff exporter:
   1. Download TensorRT 3.0.4 for Ubuntu 16.04 and CUDA 9.0 tar package from https://developer.nvidia.com/nvidia-tensorrt-download.
   2. Extract archive:
        ```
        tar -xzf TensorRT-3.0.4.Ubuntu-16.04.3.x86_64.cuda-9.0.cudnn7.0.tar.gz
        ```
   3. Install uff python package using pip:
        ```
        sudo pip3 install TensorRT-3.0.4/uff/uff-0.2.0-py2.py3-none-any.whl
        ```
5. Clone and build this project:
    ```
    mkdir ~/workspace
    git clone https://github.com/crespum/oscw18-edge-ai.git
    cd oscw18-edge-ai/jetson
    mkdir build
    cd build
    cmake ..
    make 
    cd ..
    ```
6. Get dependencies:
```
mkdir -p ~/workspace/tensorflow

# Clone TensorFlow source and models repo
cd ~/workspace/tensorflow
git clone https://github.com/tensorflow/tensorflow --branch v1.7.1
git clone https://github.com/tensorflow/models
```
7. Compile a model:
```
cd ~/workspace/oscw18-edge-ai/jetson/
export TF_SRC_PATH=~/workspace/tensorflow/tensorflow
export TF_MODELS_PATH=~/workspace/tensorflow/models
make VERSION=v1 compile
```

## Running inference
```
cd ~/workspace/oscw18-edge-ai/jetson/
export TF_SRC_PATH=~/workspace/tensorflow/tensorflow
export TF_MODELS_PATH=~/workspace/tensorflow/models
make infer
```

We can use two parameters:
* `VERSION` to select the Inception version to compile (v1-v4)
* `INPUT` to select on which we want to run inference. A sample image has been uploade to the repo too.
We could for instance use `make VERSION=v4 INPUT=foo.jpg`

The output should look like this:
```
$ make infer

Running inferences on ../data/pic_002.jpg...
(./build/classify_image/classify_image ../data/pic_002.jpg ./model/v3/inception_v3.plan ./model/v3/labels.txt input InceptionV3/Predictions/Reshape_1 inception)
Loading TensorRT engine from plan file...
Preprocessing input...
Executing inference engine...

The top-5 indices are: 288 283 282 286 284 
Which corresponds to class labels: 
0. 288:lynx, catamount
1. 283:tiger cat
2. 282:tabby, tabby cat
3. 286:Egyptian cat
4. 284:Persian cat
```
