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
