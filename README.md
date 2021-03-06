# FusED
This repo consists of the source code for FusED and the examples of the fusion errors it has found.


## Fusion Error Examples

We show the following fusion error examples. The left window of each pair shows an accident caused by the fusion component and the right window shows the accident would be avoided if the fusion method is replaced to "best-sensor fusion"(defined in the paper) during the pre-crash period in a counter-factual world.


1.OpenPilot (with DEFAULT - the default fusion method as the initial fusion method):
The ego car avoids its collision with the green vehicle ahead after the replacement.
<img src="gif_demos/nsga2_op_446_merge.gif" width="100%" height="100%"/>


2.OpenPilot (with DEFAULT - the default fusion method as the initial fusion method):
The ego car avoids its collision with the green vehicle moving out after the replacement.
<img src="gif_demos/random_op_44_merge.gif" width="100%" height="100%"/>


3.OpenPilot (with MATHWORKS - a kalman-filter based fusion method as the original fusion method):
The ego car avoids its collision with the police car cutting in from the right lane after the replacement.
<img src="gif_demos/random_29_mathwork.gif" width="100%" height="100%"/>


4.OpenPilot (with MATHWORKS - a kalman-filter based fusion method as the original fusion method):
The ego car avoids its collision with the red vehicle cutting in from the right lane after the replacement.
<img src="gif_demos/259_mathwork.gif" width="100%" height="100%"/>


## Build from Source

### Requirements
* OS: Ubuntu 20.04
* CPU: at least 6 cores
* GPU: at least 6GB memory
* Openpilot 0.8.5 (customized)
* Carla 0.9.11


### Directory Structure
~(home folder)
```
├── openpilot
├── Documents
│   ├── self-driving-cars (created by the user manually)
│   │   ├── FusED
│   │   ├── carla_0911_rss
```
Note: one can create link for these folders at these paths if one cannot put them in these paths.

### Install OpenPilot 0.8.5 (customized)
In `~`,
```
git clone https://github.com/FusionFuzz/openpilot.git
```
In `~/openpilot`,
```
./tools/ubuntu_setup.sh
```
In `~/openpilot`, compile Openpilot
```
scons -j$(nproc)
```

#### Common Python Path Issue
Make sure the python path is set up correctly through pyenv, in particular, run
```
which python
```
One should see the following:
```
~/.pyenv/shims/python
```
Otherwise, one needs to follow the instruction by displayed after running
```
pyenv init
```

#### Common OpenCL Issue
Your enviroment needs to support opencl2.0+ in order to run `scons` successfully (when using `clinfo`, it must show something like  "your OpenCL library only supports OpenCL <2.0+>")



### Install Carla 0.9.11
In `~/Documents/self-driving-cars`,
```
curl -O https://carla-releases.s3.eu-west-3.amazonaws.com/Linux/CARLA_0.9.11_RSS.tar.gz
mkdir carla_0911_rss
tar -xvzf CARLA_0.9.11_RSS.tar.gz -C carla_0911_rss
```

In `~/Documents/self-driving-cars/carla_0911_rss/PythonAPI/carla/dist`,
```
easy_install carla-0.9.11-py3.7-linux-x86_64.egg
```

#### Install additional maps
In `~/Documents/self-driving-cars/carla_0911_rss`,
```
curl -O https://carla-releases.s3.eu-west-3.amazonaws.com/Linux/AdditionalMaps_0.9.11.tar.gz
mv AdditionalMaps_0.9.11.tar.gz Import/
./ImportAssets.sh
```

### Installation for FusED
In `~/Docuements/self-driving-cars`,
```
git clone https://github.com/FusionFuzz/FusED.git
```

In `~/openpilot/tools/sim/op_script`,
```
pip3 install -r requirements.txt
```

Install pytorch
```
pip3 install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
```


### Run FusED
In `~/Docuements/self-driving-cars/FusED`,
```
python ga_fuzzing.py --simulator carla_op --n_gen 10 --pop_size 50 --algorithm_name nsga2 --has_run_num 500 --episode_max_time 120 --only_run_unique_cases 0 --objective_weights 1 0 0 0 -1 -2 0 -m op --route_type 'Town06_Opt_forward'
```


### Rerun scenarios using the best sensor prediction
Move all the subfolders indexed of previously run simulation results in `~/Docuements/self-driving-cars/FusED/run_results_op` to `openpilot/tools/sim/op_script/rerun_folder`, then in `openpilot/tools/sim/op_script`,
```
python rerun_carla_op.py -p rerun_folder -m2 best_sensor -w 2.5
```


# Reference
This repo is partially built on top of [pymoo](https://github.com/msu-coinlab/pymoo)
