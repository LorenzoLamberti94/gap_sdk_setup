# How to setup GAP_SDK 3.9.1 for nano-drones

## Prerequisites

Opencv3.2. Unfortunately it is not ufficially supported anymore. But here's how to install it

this is a repo that allows to do so: https://gist.github.com/syneart/3e6bb68de8b6390d2eb18bff67767dcb 
To install execute the following

```
# wget -O - https://gist.githubusercontent.com/syneart/3e6bb68de8b6390d2eb18bff67767dcb/raw/OpenCV3.2withContrib.sh | bash
```

#### check if installed:
```
 dpkg -l | grep libopencv
```

### Possible errors
#### 1. CUDA
```
CMake Error: The following variables are used in this project, but they are set to NOTFOUND.
Please set them or make sure they are set and tested correctly in the CMake files:
CUDA_nppi_LIBRARY (ADVANCED)
```
Solution: bad version of cuda. i completely uninstalled cudadevtoolkit

#### 2. LAPACKE missing

```
fatal error: LAPACKE_H_PATH-NOTFOUND/lapacke.h: No such file or directory
#include "LAPACKE_H_PATH-NOTFOUND/lapacke.h"
```
found this solution working: https://github.com/YuvalNirkin/face_swap/issues/51
```
I needed to change the file pointer here
nano ~/opencv-3.2.0/build/opencv_lapack.h

changing line
#include "LAPACKE_H_PATH-NOTFOUND/lapacke.h" 
into
#include "/usr/include/lapacke.h"
```

## Minimal install
##############################################################################
##############################	MINIMAL	##############################
##############################################################################

```
git clone https://github.com/GreenWaves-Technologies/gap_sdk.git
cd gap_sdk
git submodule update --init --recursive
```

```
pip install -r requirements.txt
pip install -r tools/nntool/requirements.txt
```

```
source sourceme.sh   (source configs/ai_deck.sh)
make sdk
```

```
make gap_lib
make gap_tools
```

Random dependencies that might lack:
openCV:  	`sudo apt-get install libopencv-dev`
configparse: 	`sudo apt-get install -y python-configparser`

```
pip install pyelftools
pip install argcomplete
```

RISCV Toolchain

```
git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain_ubuntu_18.git
cd ~/gap_riscv_toolchain_ubuntu_18
./install.sh
```

if you chose a custom path for install, add this to your .bashrc file:
`export GAP_RISCV_GCC_TOOLCHAIN="custom/path/that/you/chose"`

## FULL install (first time)

#########################################################################################################
##############################     FULL GUIDE    #############################################
#########################################################################################################

Full GWT guide can be found at: https://greenwaves-technologies.com/setting-up-sdk/

Tip: create a conda environment (and call it gap_sdk) to install all the packets needed by the sdk (pip commands below)
conda create --name gap_sdk python==3.6.10 numpy cython
conda activate gap_sdk

PREREUISITES: the following packages needed to be installed.
sudo apt-get install -y build-essential git libftdi-dev libftdi1 doxygen python3-pip libsdl2-dev curl cmake libusb-1.0-0-dev scons gtkwave libsndfile1-dev rsync autoconf automake texinfo libtool pkg-config libsdl2-ttf-dev

#Toolchain

```
git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain_ubuntu_18.git
cd ~/gap_riscv_toolchain_ubuntu_18
./install.sh
```
	if you chose a custom path for install, add this to your .bashrc file:
	`export GAP_RISCV_GCC_TOOLCHAIN="custom/path/that/you/chose"`

GAP SDK Install
```
git clone https://github.com/GreenWaves-Technologies/gap_sdk.git
cd gap_sdk
# you change version with this command
git checkout release-v3.9.1
git submodule update --init --recursive
source sourceme.sh
```

#IMPORTANT: remember your choiche, you will have to source in every terminal you use always the same board to make the sdk work!!!!!!!!!!!!!!
#IMPORTANT#2 : always run the sourceme.sh in a fresh new terminal. Never source this file two times in the same terminal (might have issues)

```
pip install -r requirements.txt
cd tools/nntool/
pip install -r requirements.txt
cd ../..
```

```
make sdk
```

### enable Olimex
```
sudo cp $HOME/work/gap_sdk/tools/gap8-openocd/contrib/60-openocd.rules /etc/udev/rules.d
sudo udevadm control --reload-rules && sudo udevadm trigger
sudo usermod -a -G dialout <username>
```

then log out and log in again.

### BONUS: INSTALL LIBOPENCV3.2
this is an old version of libopencv, a requirement for gap sdk 3.9.1, no longer supperted by apt by default.
You need to find the right PPA for your Linux OS

this bash script does everything for you
https://gist.github.com/LorenzoLamberti94/2f35d844121a63558c425618ea12ceef

just run
`wget -O - https://gist.githubusercontent.com/syneart/3e6bb68de8b6390d2eb18bff67767dcb/raw/OpenCV3.2withContrib.sh | bash`



# TEST: To check everything works

```
cd examples/pmsis/helloworld
make clean all run platform=gvsoc
Use Gap sdk a second time:
cd gap_sdk
source sourceme.sh
```

#select the same board you selected while installing
Doublecheck gap_sdk version:
```
git log
```
