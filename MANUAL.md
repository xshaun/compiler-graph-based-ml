# Compile and Build

```bash
# module: anaconda/2019.10; anaconda1.7.2; conda4.7.12

CONDA_VENV_PATH=/nobackup/scxs/conda-venv-python3.6

#
# create virtual environment
# python version <3.8 >=3.6
conda create -y -p $CONDA_VENV_PATH python=3.6 matplotlib pandas numpy
conda activate $CONDA_VENV_PATH

#
# bazel
# manually install tensorflow with pip as author's said
#
conda install -y -c conda-forge bazel
pip install  tensorflow==1.14.0 --user

#
# a series of bugs
#
#>. failed building wheel for grpcio
conda install -y -c conda-forge cxx-compiler 

#>. ld.gold: error: cannot find -ltinfo: not found libtinfo.so.6 
conda install -y -c conda-forge ncurses
LD_LIBRARY_PATH=$CONDA_VENV_PATH/lib/:$LD_LIBRARY_PATH
LIBRARY_PATH=$CONDA_VENV_PATH/lib/:$LIBRARY_PATH

conda update -y --all

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

### Cython runtime is not compitable with the current XX version
```bash
# upgrade pip packages to overwrite the default version
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 pip install -U --user 
 ```

### not sure the LLVM is comptible with the current system os or platform architecture
run the following command and see if errors occur
```bash
<bazel_path>/external/<clang-llvm-10.0.??>/bin/llvm-config --system-libs
```
### libz3.so not found
```bash
git clone https://github.com/Z3Prover/z3.git
python scripts/mk_make.py --prefix=$CONDA_VENV_PATH/
cd build
make
make install
```
### libz3.so not found

#>. fix dependency. clang+llvm 10.0.0 (sb, is using centos and restricting software installation)
conda install -y -c conda-forge clang==10.0.0 clang-tools==10.0.0 clang_osx-64==10.0.0  clangdev==10.0.0 clangxx_osx-64==10.0.0 libclang==10.0.0 python-clang==10.0.0 llvm==10.0.0 libllvm10==10.0.0 llvm-tools==10.0.0 llvmdev==10.0.0 libunwind==1.3.1 lld=10.0.0 libcblas==3.8.0  doxygen==1.8.18


### download dataset

```bash
wget https://www.dropbox.com/s/cw25t4cls6otel5/llvm_ir_20.06.01.tar.bz2 && tar jxvf llvm_ir_20.06.01.tar.bz2
wget https://www.dropbox.com/s/f7j2qbo6hobckqy/graphs_20.06.01.tar.bz2 && tar jxvf graphs_20.06.01.tar.bz2
wget https://www.dropbox.com/s/ha0lp343an336fp/labels_reachability_20.06.01.tar.bz2 && tar jxvf labels_reachability_20.06.01.tar.bz2
wget https://www.dropbox.com/s/cl4kuak4kds9yuc/labels_domtree_20.06.01.tar.bz2 && tar jxvf labels_domtree_20.06.01.tar.bz2
wget https://www.dropbox.com/s/yxqzmp4vvzycxje/labels_datadep_20.06.01.tar.bz2 && tar jxvf labels_datadep_20.06.01.tar.bz2
wget https://www.dropbox.com/s/qvhekvovx6n1ino/labels_liveness_20.06.01.tar.bz2 && tar jxvf labels_liveness_20.06.01.tar.bz2
wget https://www.dropbox.com/s/a8tkugnu6mq64kq/labels_subexpressions_20.06.01.tar.bz2 && tar jxvf labels_subexpressions_20.06.01.tar.bz2
wget https://www.dropbox.com/s/o3qopbyhf2p5pf2/dataflow_logs_20.06.01.tar.bz2 && tar jxvf dataflow_logs_20.06.01.tar.bz2
```
