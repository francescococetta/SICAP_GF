# Sea Ice Parameter Sensitivity and Optimization

## Overview

This repository contains scripts used to evaluate the performance of **sea-ice model experiments** by comparing model outputs with multiple **observational datasets**.
The goal is to quantify model–observation discrepancies and estimate **optimal parameter perturbations** that improve the model representation of Arctic sea ice.

The workflow computes a **cost function** for different experiments and applies a **linear inverse method** to estimate optimal parameter values.

The analysis period covers **2011–2020**.

---

## Scientific Objective

Sea-ice models contain several poorly constrained physical parameters.
This project evaluates how perturbations in selected parameters affect the simulation of:

* Sea ice concentration
* Sea ice thickness
* Ice drift (zonal and meridional components)

Model simulations are compared against satellite observations to identify parameter configurations that minimize the model–data misfit.

---

## Observational Datasets

The following observational products are used:

| Variable              | Dataset          | Description                             |
| --------------------- | ---------------- | --------------------------------------- |
| Sea ice concentration | OSI-SAF          | Satellite-derived sea ice concentration |
| Sea ice thickness     | CryoSat-2 / SMOS | Merged sea ice thickness product        |
| Ice drift (u)         | OSI-SAF drift    | Zonal sea-ice velocity                  |
| Ice drift (v)         | OSI-SAF drift    | Meridional sea-ice velocity             |

All datasets are:

* restricted to **latitudes north of 55°N**
* interpolated onto the **ORCA025 model grid**
* limited to the **2011–2020** period.

---

## Model Experiments

The analysis compares a reference simulation with several sensitivity experiments, each modifying a single sea-ice model parameter.
This approach allows the response of the model to individual physical processes to be isolated and quantified.

| Experiment   | Parameter                           | Description                                                         |
| ------------ | ----------------------------------- | ------------------------------------------------------------------- |
| `reference`  | —                                   | Baseline simulation used as control configuration                   |
| `r_snw`      | Snow grain radius                   | Controls snow optical properties and affects radiative transfer     |
| `rsnw_mlt`   | Melt snow grain radius (x10-6 m)    | Parameter influencing snow albedo evolution during melt             |
| `emissivity` | Surface emissivity                  | Affects longwave radiation exchange at the ice–atmosphere interface |
| `hi_ssl`     | Ice–snow interface parameter (m)    | Influences thermodynamic coupling between ice and snow              |
| `Pstar`      | Ice strength parameter (N/m2)       | Determines the magnitude of internal ice stresses in the rheology   |
| `dragio`     | Ice–ocean drag coefficient          | Controls momentum transfer between ocean and sea ice                |
| `ksno`       | Snow thermal conductivity (W/m/°C)  | Regulates heat flux through the snow layer                          |
| `rhos`       | Snow density (kg/m3)                | Influences snow mass, insulation, and snow–ice conversion           |
| `iceruf`     | Ice roughness length (m)            | Affects turbulent exchange with the atmosphere                      |
| `rhoi`       | Ice density (kg/m3)                 | Influences buoyancy and freeboard                                   |
| `astar`      | Lead closing parameter              | Controls ice redistribution during deformation                      |

Each experiment perturbs only one parameter relative to the reference simulation, allowing the sensitivity of the model to that parameter to be evaluated.

---

## Methodology

### 1. Data preprocessing

* Observational and model fields are masked to **latitudes > 55°N**.
* Fields are flattened into vectors to simplify matrix operations.
* Missing values are removed before computing statistics.

### 2. Scaling

Observations are normalized using

$$
\frac{1}{\sqrt{N}}
$$

where (N) is the number of valid observations.
This ensures that datasets with different spatial coverage contribute comparably to the cost function.

### 3. Cost Function

The misfit between model and observations is calculated as:

$$
J = \sum \frac{W}{R}(Y_d)^2
$$

where:

* (Y_d) = model − observation
* (R) = observational variance
* (W) = spatial weights

The total cost is obtained by summing contributions from all observational datasets.

### 4. Parameter Optimization

Optimal parameter perturbations are estimated using a **linear inverse approach**:

$$
P = (Q^{-1} + G^T R^{-1} G)^{-1}
$$

$$
\eta = P G^T R^{-1} Y_d
$$

where:

* (G) = model response to parameter perturbations
* (R) = observational covariance
* (Q) = parameter covariance

Optimized parameter values are then derived as:

$$
p_{opt} = p_{ref} + (p_{pert} - p_{ref}) \eta
$$

---

## Requirements

The code is written in **Python 3** and requires the following packages:

```
numpy
xarray
pandas
netCDF4
matplotlib
```

You can install them with:

```
pip install numpy xarray pandas netcdf4 matplotlib
```

---

## Running the Analysis

1. Clone the repository:

```
git clone https://github.com/username/repository-name.git
```

2. Navigate to the repository:

```
cd repository-name
```

3. Open the notebook:

```
jupyter notebook script_structure.ipynb
```

4. Run all cells to reproduce the analysis.

---

## Outputs

The workflow produces:

* Cost function diagnostics for each experiment
* Optimal parameter estimates
* Figures comparing experiment performance
* Bar plots of observational contributions to the cost function

Typical runtime is **~25 minutes**, depending on system performance.

---

## Notes

* Observational datasets must already be **regridded to the ORCA025 grid**.
* Large model outputs are not included in the repository.
* Paths to datasets should be modified according to your local system.

---

## Author

Cocetta F. (2026)
