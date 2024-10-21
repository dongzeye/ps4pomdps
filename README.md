# Posterior Sampling-based Online Learning for Episodic POMDPs
This repository contains the official implementation of Posterior Sampling-based Online Learning for Episodic POMDPs.


## Requirements
NOTE: This repository is fully tested on Linux (gcc 9.2.0) and [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/install) (gcc 9.4.0). However, there might be compilation errors for installing the POMDP planner [SARSOP](https://github.com/AdaCompNUS/sarsop) natively on Windows or MacOS.

### (External) SARSOP Planner Installation
To install requirements, please first install the POMDP planner [SARSOP](https://github.com/AdaCompNUS/sarsop) by running:

```setup_sarsop
git clone https://github.com/AdaCompNUS/sarsop.git
cd sarsop/src
make
```
We will need to use the binary code of the POMDP solver in `./sarsop/src/pomdpsol`.

*(Optional) In case of compilation errors*, please try installing `gcc-9` and `g++-9` and then compile with:
```make_sarsop_gcc9
make clean
make CC="gcc-9" CXX="g++-9"
```

### Setup Python Environment
We recommend using [Conda](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) to setup a standalone Python environment for this project: 
```conda
conda create -n ps4pomdp python=3.11
conda activate ps4pomdp
```

Finally, install necessary package with `pip` (note that `requirements.txt` is located under the main project directory):
```install_req
pip install -r requirements.txt
```

*(Optional) In case the above commands do not work in your system*, try directly installing the necessary packages with the following command: 
```direct_install
pip install -U numpyro[cpu] jax[cpu] gymnasium numpy pandas seaborn ipykernel  
```


### Experiment Configs for Reproducibility
For full reproducibility, we generate and store the configurations for each expeirment. Such experiments can be found under `./experiments/configs/`. To generate new experiment ocnfigurations, we provide a simple notebook `generate_configs.ipynb`.

## Running Experiments

For a very brief test run of the experiment (in `Tiger`), please run the following command:
```test_run
python run_tiger.py --exp_name=tiger_theta3_discounted --run_id=0 --max_episodes=10 --verbose
```

The test-run strictly following the experiment configuration in `./experiments/tiger_theta3_discounted.json`. It will fetch the `global` config and the local config with key `0`, which is pre-generated for run 0 of the experiment named `tiger_theta3_discounted`. 

For a complete run of `Tiger` for each $\theta$ reported in the manuscript, you may run the following command. NOTE: This may take a few hours depending on your hardware.
```run_tiger
python run_tiger.py --exp_name=tiger_theta2_discounted --run_id=0
python run_tiger.py --exp_name=tiger_theta3_discounted --run_id=0
python run_tiger.py --exp_name=tiger_theta4_discounted --run_id=0
```

The final results are averaged over independent runs with `run_id = 0, 1, ..., 19`.

To run experiments for `RiverSwim` or `RandomPOMDP` (or "Random Sparse Reward POMDP"), you may use the generic experiment script with the following pattern:
```run_experiments
python3 run_experiment.py --exp_name=${exp_name} --run_id=${i}
```
where the `exp_name` matches a config file in `./experiments/configs/` and the run ID `i` can be found inside the config file. 


## Evaluation
For `Tiger`, figures can generated with the following command:
```plot_tiger
python eval_tiger.py
```
The figures will be saved under `./figures/`. Some existing figures are placed under `./figures/in_manuscript/`. 

For plotting additional `Tiger` experiments (along with their labels in the plots) may be added by modifying `EXP_NAMES` and `LABELS` at the constants section of `eval_tiger.py`.

For general POMDPs (like `RiverSwm` and `RandomPOMDP`), we first need to evaluate the policy values using Monte Carlo method, which can be run with this pattern:
```eval_policy
python -um evals.eval_policy --exp_name=${exp_name} --n_samples=1000
```

Then, figures can be drawn with:
```eval_all
python eval_all.py
```
By default, this script (`eval_all.py`) will use the existing plotting data for each experiment, provided under `./figures/<exp_name>`. Feel free to replace the paths to other files (e.g., `eval_results.csv` generated from the polication evaluatoin script above) for a local run. Figures in the main text can be generated by subsetting plotting data to a particular environment (or agent).

## Acknowledgements
We use [SARSOP](https://github.com/AdaCompNUS/sarsop) for solving the POMDP planning problems and adapted the code in [pomdp_py](https://github.com/h2r/pomdp-py) (with slight modifications) for interfacing with the C++ implementation of SARSOP. 
