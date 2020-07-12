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

### download dataset

```bash
wget -c https://www.dropbox.com/s/cw25t4cls6otel5/llvm_ir_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/f7j2qbo6hobckqy/graphs_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/ha0lp343an336fp/labels_reachability_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/cl4kuak4kds9yuc/labels_domtree_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/yxqzmp4vvzycxje/labels_datadep_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/qvhekvovx6n1ino/labels_liveness_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/a8tkugnu6mq64kq/labels_subexpressions_20.06.01.tar.bz2 | tar jxvf
wget -c https://www.dropbox.com/s/o3qopbyhf2p5pf2/dataflow_logs_20.06.01.tar.bz2 | tar jxvf
```
