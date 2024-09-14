# 1.0 Overview

**CosmoTherm** is a flexible C++ code for solving cosmological thermalization scenarios related to matter heating and photon injection. It was first developed by JC at CITA in 2011 and is based on the Ph.D. work carried out by JC at MPA under the supervision of Prof. Rashid Sunyaev. Over the last decade, CosmoTherm was significantly augmented and extended, in particular by the ERC-funded CMBSPEC team during the years 2017-2023, with major contributions from Sandeep Acharya, Boris Bolliet, Jiten Dhandha, Bryce Cyr, Thomas Kite, Andrea Ravenni, and Abir Sarkar.

CosmoTherm can be used to predict the average spectral distortion signals for a wide range of standard and new physics scenarios (e.g., damping of acoustic modes, adiabatic cooling process, cosmic strings, and decaying particles). It also has a new framework for treating spectral distortion anisotropies, which was more recently developed. Furthermore, it features a Python interface and simple likelihood codes to quickly assess whether a considered distortion scenario is already in tension with existing data from COBE/FIRAS. For monopole distortion scenarios, a movie can be activated to follow the distortion evolution in real time, a feature that can come in handy when developing new modules and scenarios.

The main CosmoTherm paper is [JC & Sunyaev, MNRAS, 2012](https://ui.adsabs.harvard.edu/abs/2012MNRAS.419.1294C/abstract), which we kindly ask you to cite when the code is used. However, many powerful features and modules were developed later, and in each of the sections below, we provide more in-depth references that should be considered for citation when using the related modules.

## 1.1 About this documentation

This documentation is a living document that will be updated continuously. It will not attempt to cover all the details of the code nor cover all releases. However, in each release, a current snapshot of this document will be provided for tracking. Please get in touch with any issues or suggestions of topics to cover in more detail.

## 1.2 Downloading CosmoTherm

CosmoTherm can be cloned using:

```bash
git clone git@bitbucket.org:Jacetoto/cosmotherm.rel_corr.git
```

The main branch is `CosmoTherm`. Unreleased new features will be found in `CosmoTherm-dev`, but they should be used cautiously. Various users may also provide their own individual branches. Please contact them directly with questions.

## 1.3 Main directory structure and modules

The main **CosmoTherm** driver is implemented in `CosmoTherm.cpp`, which is found at the top level of the CosmoTherm folder. This file primarily manages the user inputs and various run modes. As such, it contains nothing but setup-related features and some backward communication with the user. Many of the related routines and variables are defined and implemented in the folder `./CosmoTherm_Plugins`.

Various example run files (usually ending in `.ini`) come with CosmoTherm and are located in the folder `./runfiles`. These can be used with the command line setup and control the different run modes and distortion scenarios as well as outputs and the verbosity level. A detailed walkthrough of the various parameters will be given in **Section 2.3**. Many comments and instructions are also present in the parameter files, and some of the variable names are hopefully self-explanatory. Features of the Python interface (hosted in `./Python-wrapper`) will be explained in **Section 7**, and they have been primarily developed by Jiten Dhandha.

Before turning to the core components of CosmoTherm, an important folder is `./Tools`. It hosts many standalone routines (e.g., ODE and PDE solvers, Hydrogen Atom setup, Parsers, and simple routines) that should be of broad interest also for other applications. These tools were applied to many problems beyond the thermalization calculations (e.g., the cosmological recombination problem) and have been rigorously tested. While in most cases, one does not expect to need any changes in these routines, standard cosmology setups and various solvers are found there for expert-level developments. We will detail some of the tools in **Section XX**.

The real workhorses of CosmoTherm are located in the folders `./Thermalization_Module` for the average spectral distortion scenarios (see **Section 4**) and `./frequency_hierarchy_module` for anisotropic distortions (see **Section 6**). An outdated CMB anisotropy module that was originally used to compute the spectral distortions created by the dissipation of acoustic modes is hosted in `./Anisotropy_Module`, and will be briefly discussed in **Section 5**. The frequency hierarchy module is the most recent addition to the capabilities of CosmoTherm and was developed together with Tom Kite and Andrea Ravenni.


# 2.0 Quick Start

In this section, we briefly explain how to install and run CosmoTherm out of the box. More details will be given in the subsequent sections. Simple instructions are also given in `./README.rst` and the parameter files located in the `./runfiles` directory.

## 2.1 Installation

The installation is controlled by the Makefiles:

- `./Makefile`
- `./Makefile.in`

Here, compilers are set and the locations of libraries are defined. The code depends on GSL, Boost, and (optionally) Grace. If not already present, all these can usually be installed using standard package management software (e.g., Homebrew, Macports, apt-get, etc.), which we highly recommend over manual installation. GSL and Boost provide simple routines and functionality beyond the standard C++ libraries and must be present. Grace can be optionally activated to provide functionality for showing real-time movies, which can be very educative and useful for debugging and development.

Assuming all the libraries are present, CosmoTherm can be compiled using the command:

```bash
./make
```

The default compiler is `g++`, which is set in `./Makefile.in` as `CC=g++`. If another compiler is to be used, this variable should be changed, or the make command should be invoked as:

```bash
./make CC=my_preferred_compiler
```

To clean the compilations, various defaults are defined in `./Makefile` and provide different levels of tidiness. For example, by typing:

```bash
./make tidy
./make clean
./make cleanPDE
```

Common defaults for the GSL and Boost installation are assumed, but to set the library and include paths, respectively, change the variables:

- `GSL_LIB_PATH`
- `GSL_INC_PATH`
- `BOOST_LIB_PATH`
- `BOOST_INC_PATH`

inside `./Makefile.in`. To activate Grace support, uncomment:

```bash
USE_GRACE = -D GRACE_DEFINED
```

and modify the paths `GR_LIB_PATH` and `GR_INC_PATH` as needed, again inside `./Makefile.in`. Additional OpenMP features can be activated by uncommenting:

```bash
USE_OPENMP = -D OPENMP_ACTIVATED
```

This information covers all the main installation requirements and cases should changes be needed.

## 2.2 Running CosmoTherm

Once compiled, the code can be either called from the command line using:

```bash
./CosmoTherm runfiles/parameters.ini
```

or via the Python interface (see Sect. 7). Instructive example parameter files are provided in `./runfiles/Examples` for simple illustration. The outputs will be written into `./outputs`. If the folder does not exist, it will be created. The output folder can also be changed as one of the runfile parameters.

## 2.3 Parameter File

The parameter file is read by a general-purpose parser routine (`./Tools/Simple_routines/parser.cpp`) using tags for the various parameters. These tags must be unique and can include spaces. Most communication with the parameter file is handled in `./CosmoTherm_Plugins/initial_setup.cpp`, as will be explained further below. The parameter files do not have to follow a specific structure and need only set parameters that should not take their internal default values. A complete example is given by `./runfiles/parameter.ini`, which in principle allows running all aspects of the code with only a few line changes.

![Simple Loading Animation](https://media.giphy.com/media/3o7qE1YN7aBOFPRw8E/giphy.gif)


### 2.3.1 Runmodes

The template parameter file starts by specifying the runmode. The following runmodes are of most common interest:

- **default**: runs individual heating modes. This is the most useful runmode for applications.
- **default-batch**: launches multiple parallel runs on single threads.
- **default-loop**: computes a loop over a chosen parameter.
- **default-Analysis**: runs like the default mode but ends with likelihood evaluations.
- **visibility**: computes visibility for single heating and decay.
- **Greens**: delta function injection of energy at $z_h$ for a range of redshifts.
- **Greens-nu**: delta function injection of photons at $nu_i$ and $z_i$ for a range of frequencies.
- **Greens-nu-z**: similar to 'Greens_nu' but in redshift.

Other setups are implemented in `./CosmoTherm.cpp` but are not further explained in this section.

### 2.3.2 Frequency Grid and Redshift Settings

The next block of parameters specifies the frequency grid and redshift settings. The code uses the dimensionless frequency $x = \frac{h\nu}{kTg}$. The frequency grid parameters are:

- `xmin`: lower frequency of the photon spectrum.
- `xmax`: upper frequency.
- `npts`: number of frequency bins.

For the time variable, which is redshift, the settings are:

- `zstart`: Starting redshift.
- `zend`: Final output redshift.
- `zlate`: Redshift below which the late-time solver is used.

### 2.3.3 Cosmology Parameters

The next block of parameters specifies the background cosmology, set up in the cosmology class `./Tools/Cosmology/Cosmos.cpp`. Parameters include:

- `T0`: Present-day CMB temperature
- `Yp`: Helium mass fraction
- `N_eff`: Effective relativistic degrees of freedom
- `Omega_m`: Total matter density
- `Omega_b`: Baryon density
- `Omega_L`: Cosmological constant
- `h100`: Reduced Hubble parameter

CosmoTherm solves the ionization history using Recfast++, based on CosmoRec.

### 2.3.4 Overall Physics Settings

This block of parameters controls the general physics setup for each run. For example:

- `include Hubble cooling`: Controls Hubble cooling term in the electron temperature equation.
- `emission/absorption mode`: Controls Bremsstrahlung (BR) and double Compton (DC) emission terms.
- `switch off CMB background`: Allows switching the effect of the CMB blackbody on photon field evolution.
- `include Lyc absorption`: Switches the effect of Ly-c absorption on spectral distortion.
  
### 2.3.5 Solver Selection and Setups

With `solver selection`, you can choose the specific solver for the problem. Standard solvers include `PDE` and `PDE-non-linear`, while advanced computations use `exact` and `exact-Msc`.

### 2.3.6 Heating Modes and Scenarios

Settings for selected heating modes are chosen using the `Heating mode` tag. Available modes include particle annihilation, decaying particles, single energy release, and user-defined heating rates.

Refer to the example runfiles and relevant papers for detailed explanations.

### 2.3.7 Green’s Function Setups

Control parameters for thermalization Green's function are specified with `Greens: [...]`. Activate the setup by choosing the appropriate runmode starting with `Greens`.

### 2.3.8 Visibility Setups

Control parameters for the distortion visibility function are specified with `visibility: [...]`. Use the `visibility-batch` mode for parallel computation.

### 2.3.9 The 21cm Module

The global 21cm signal can be computed using a module added by Jiten Dhandha in 2021. Parameters start with `21cm modelling: [...]`.


# 3.0 Structures and Main Classes

# 4.0 Thermalization Module

# 5.0 Anisotropy Module

The anisotropies module was originally written by Jens Chluba (see results in the [2x2 paper](https://arxiv.org/abs/1202.0057)). Its main goal was to implement the early-universe Boltzmann equation hierarchy with accurate predictions for primordial $ \mu $- and $ y $- distortions.

This module was reworked by Thomas Kite.


