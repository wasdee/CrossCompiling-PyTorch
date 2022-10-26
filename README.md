# CrossCompiling-PyTorch
My note on trying to cross compiling Pytorch for Jetson Xavier NX

> TLDR; I failed.

## Main Idea
is it possible to using [Jetpack Crosscompile Container](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/jetpack-linux-aarch64-crosscompile-x86) to compile any pytorch versions?


If I could do this it would be a very fast way of iteration development since I have a very powerful x86-64 PC. 


The worst case I just fall back to compile directly on the device.

## Googling
1. [How to compile pytorch on jetson, kinda official](https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048)
2. [Another guy with simple but might not work.](https://gist.github.com/soumith/45f61db3379db67414814ad9d5f2a427)

### Extra
1. [How to compile torchvision on jetson](https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048/15)

## Naive Tries
1. Just compile on the container, `export all the envs that works` -> not working, result in x86 build
2. Try to add Toolchain, combining the example file with modified `CMAkeList` file -> not working too.

```
$ export USE_NCCL=0
$ export USE_DISTRIBUTED=0                    # skip setting this if you want to enable OpenMPI backend
$ export USE_QNNPACK=0
$ export USE_PYTORCH_QNNPACK=0
$ export TORCH_CUDA_ARCH_LIST="7.2;8.7"   # or "7.2;8.7" for JetPack 5 wheels for Xavier/Orin

$ export PYTORCH_BUILD_VERSION=1.12.0  # without the leading 'v', e.g. 1.3.0 for PyTorch v1.3.0
$ export PYTORCH_BUILD_NUMBER=1

(remember to re-export these environment variables if you change terminal)

Build wheel for Python 2.7 (to pytorch/dist)

$ sudo apt-get install python-pip cmake libopenblas-dev libopenmpi-dev 
$ pip install -U pip

$ sudo pip install -U setuptools
$ sudo pip install -r requirements.txt

$ pip install scikit-build --user
$ pip install ninja --user

$ python setup.py bdist_wheel

Build wheel for Python 3.6 (to pytorch/dist)

$ sudo apt-get install python3-pip cmake libopenblas-dev libopenmpi-dev 

$ pip3 install -r requirements.txt
$ pip3 install scikit-build
$ pip3 install ninja

export CROSS_COMPILE_AARCH64=/l4t/toolchain/bin/aarch64-buildroot-linux-gnu- 
export CROSS_COMPILE_AARCH64_PATH=/l4t/toolchain 
export PYTHON3_PATH="/usr/bin/python3.8" 
export CUDACXX=/usr/local/cuda-11/bin/nvcc
CMAKE_TOOLCHAIN_FILE=/l4t/NVIDIA_VPI-2.1-samples/08-cross_aarch64_l4t/Toolchain_aarch64_l4t.cmake

# for python header /usr/include/python3.8
sudo apt-get install python3-dev

$ python3 setup.py bdist_wheel
```
## Future 
- This guy posted his [success quick and dirty 2013 note](https://fatalfeel.blogspot.com/2013/12/libtorch-cross-compile-on-aarch64-linux.html). 
