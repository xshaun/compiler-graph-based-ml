# Compile and Build

```bash
# module: anaconda/2019.10; anaconda1.7.2; conda4.7.12

#
# create virtual environment
# python version <3.8 >=3.6
conda create -y -p <venv-path> python=3.6 matplotlib pandas numpy
conda activate <venv-path>

#
# bazel
# manually install tensorflow with pip as author's said
#
conda install -y -c conda-forge bazel
pip install  tensorflow==1.14.0 --user

#
# error message: Failed building wheel for grpcio
# solution: upgrade cxx-compiler and add -std=c++11
#
conda install -y -c conda-forge cxx-compiler 

#
# this step is a little time-consuming
#
bazel test //programl/...
```

# QA

### replace llvm**ubuntu with llvm version supporting CentOS

```bash
sed -i s/clang-llvm-10.0.0-x86_64-linux-gnu-ubuntu-18.04/clang-llvm-10.0.0-aarch64-linux-gnu/g `grep -rl "clang-llvm-10.0.0-x86_64-linux-gnu-ubuntu-18.04" ./`
```
