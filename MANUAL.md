# CentOS only

```
sudo yum install -y epel-release
sudo yum install -y ca-certificates
sudo yum install -y curl
sudo yum install -y wget
sudo yum install -y g++
sudo yum install -y gcc-c++                        # Needed for building grpcio
sudo yum install -y git
sudo yum install -y python
sudo yum install -y python-devel                # Python headers needed for pybind
sudo yum install -y python3.6
sudo yum install -y python3.6-devel
sudo yum install -y unzip
sudo yum install -y zip
sudo yum install -y bzip2
sudo yum install -y m4
sudo yum install -y python3-numpy
sudo yum install -y rsync
sudo yum install -y tar
sudo yum install -y nasm
sudo yum install -y patch                              # Required by bazel workspace rules:
sudo yum install -y cmake
sudo yum install -y python3-tkinter              # python3-tk
sudo yum install -y java-11-openjdk             # openjdk-11-jdk
sudo yum install -y python36-distutils-extra # python3-distutils
sudo yum install -y mariadb-devel                # libmysqlclient-dev 
sudo yum install -y boost-devel                    # libboost-all-dev 
sudo yum install -y libtimidity-devel             # timidity
sudo yum install -y exempi-devel                  # libexempi-dev
sudo yum install -y SDL2-devel                     # libsdl2-dev
sudo yum install -y fluidsynth-devel              # libfluidsynth-dev
sudo yum install -y openal-devel                  # libopenal-dev
sudo yum install -y wildmidi-devel                # libwildmidi-dev
sudo yum install -y ocl-icd                             # ocl-icd-opencl-dev  # Needed for -lOpenCL
sudo yum install -y opencl-headers               # opencl-c-headers  # Needed for #include <CL/cl.h>
sudo yum install -y gpgme-devel                  # libgme-dev
sudo yum install -y gtk2-devel                       # libgtk2.0-dev
sudo yum install -y python36-pkgconfig       # pkg-config
yum install ncurses-devel.i686                       # libtinfo-dev
```

# CONDA Compile and Build

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

# QA & Debug

### How to manually upgrade gcc to a higher version supporting c++14
```
wget https://ftp.gnu.org/gnu/gcc/gcc-10.1.0/gcc-10.1.0.tar.gz
tar zxvf ./gcc-10.1.0.tar.gz

cd gcc-10.1.0
./contrib/download_prerequisites  
cd ..
mkdir gcc-10.1.0-build
cd gcc-10.1.0-build
../gcc-10.1.0/configure --enable-languages=c,c++ --disable-multilib --prefix=/nobackup/scxs/gcc
make
sudo make install

export PATH=/usr/local/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
```

### How to erase the old version of softwares, like gcc
```
rpm  -q gcc # search all versions of gcc installed at the current machine
rpm  -e <gcc_version> # erase old version of gcc. like gcc-4.8.5-39.el7.x86_64
```

### How to customize virutal machine to build a 'free' environment.
```
# download official cloud images, 
# like from https://cloud.centos.org/centos/7/images/
wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-Azure-1907.qcow2

# install virt-customize 
sudo apt install libguestfs-tools

# change the default password into 123456 for root
virt-customize -a CentOS-7-x86_64-Azure-1907.qcow2 --root-password password:123456
#
# [   0.0] Examining the guest ...
# [  26.9] Setting a random seed
# [  26.9] Setting passwords
# [  28.5] Finishing off

# search extra disk(s) that can be mount 
lsblk -f

# the following instructions for saving time
# sudo mount <source> <desdir>
# sudo chmod -R a+rw  <des dir>
 if [ ! -d /dev/vda2 ]; then
     sudo mkdir /dev/vda2
     sudo mount /dev/sda /dev/vda2
     sudo chmod -R a+rw  /dev/vda2
 fi
```

### Error: `/usr/bin/ld.gold: error: cannot find -ltinfo`

In CentOS environment, you have to install the 32-bit ncurses package, even at 64-bit OS. `yum install ncurses-devel.i686`


### Error: libstdc++.so.6, version `GLIBCXX*`, `CXXABI*` not found...

 The library is part of gcc compiler. If you have multiple versions of gcc, you have to make sure the library (its softlink) is compitable or corresponding with the gcc used to compile currently.
make new softlink or move ahead the include folder.

### Error: pip ** No Space
use `-d` and `-b` parameters to redirect download and build folders, like `pip3 install -r requirements.txt -b /storage/tmp/ -d /storage/tmp/`

### Error: PermissionError: [Errno 13] Permission denied: '/usr/local/lib64/python3.6'
use `--user` parameters. 

### Error: undefined reference to `llvm::***`, `std::__cxx11::`, `std::__1::__sort`, `std::__1::__less`, 
llvm was not compiled with c++11.


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

### download dataset

```bash
wget https://www.dropbox.com/s/cw25t4cls6otel5/llvm_ir_20.06.01.tar.bz2
wget https://www.dropbox.com/s/f7j2qbo6hobckqy/graphs_20.06.01.tar.bz2
wget https://www.dropbox.com/s/ha0lp343an336fp/labels_reachability_20.06.01.tar.bz2 
wget https://www.dropbox.com/s/cl4kuak4kds9yuc/labels_domtree_20.06.01.tar.bz2 
wget https://www.dropbox.com/s/yxqzmp4vvzycxje/labels_datadep_20.06.01.tar.bz2 
wget https://www.dropbox.com/s/qvhekvovx6n1ino/labels_liveness_20.06.01.tar.bz2 
wget https://www.dropbox.com/s/a8tkugnu6mq64kq/labels_subexpressions_20.06.01.tar.bz2
wget https://www.dropbox.com/s/o3qopbyhf2p5pf2/dataflow_logs_20.06.01.tar.bz2 

tar jxvf llvm_ir_20.06.01.tar.bz2
tar jxvf graphs_20.06.01.tar.bz2
tar jxvf labels_reachability_20.06.01.tar.bz2
tar jxvf labels_domtree_20.06.01.tar.bz2
tar jxvf labels_datadep_20.06.01.tar.bz2
tar jxvf labels_liveness_20.06.01.tar.bz2
tar jxvf labels_subexpressions_20.06.01.tar.bz2
tar jxvf dataflow_logs_20.06.01.tar.bz2
```
