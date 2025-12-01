# cloudy_unibo_workshop

Author: Federico Esposito, Bologna, November 2025

Web: https://github.com/federicoesposito/cloudy_unibo_workshop

Mail: f.esposito@oan.es

## Directory structure

```
cloudy_unibo_workshop
├ data
  ├ co_sleds.csv         # observed data
  ┕ sfr1_kroupa.ascii    # synthetic spectrum
├ docs                   # slides
├ grid                   # CLOUDY PDR+XDR grid files
├ notebooks              # jupyter notebook
├ pdr                    # CLOUDY PDR simulation
┕ xdr                    # CLOUDY XDR simulation
```

## Introduction to CLOUDY

Cloudy is an *ab initio* spectral synthesis code designed to model a wide range of interstellar "clouds", from H II regions and planetary nebulae, to Active Galactic Nuclei, and the hot intracluster medium that permeates galaxy clusters.

Cloudy has been in continuous development since 1978, led by Gary Ferland.

**Useful links**:
- [CLOUDY wiki](https://gitlab.nublado.org/cloudy/cloudy/-/wikis/home)
- [CLOUDY users forum](https://cloudyastrophysics.groups.io/g/Main)
- [CLOUDY youtube page](https://www.youtube.com/@Cloudy-Astroph)
- [Gary Ferland talking about CLOUDY](https://youtu.be/q3kKWFKnI8I?si=emp1AE5sGPC8DHxf)
- [CLOUDY song](https://youtu.be/L-fIO7_HJBo?si=Cybj98m2ULEUheRU)

The code works in the following way: radiation from a source strikes a gas 1D cloud (also called a *slab*), which is divided by CLOUDY into zones. In each zone, the equation of radiative transfer is computed. If the radiation source is powerful enough and the gas slab is thick enough, the gas on the surface got ionized, then it turns neutral deeper in the cloud, and then molecular, as the temperature drops.

CLOUDY computes the radiation coming from each zone, both the continuum spectrum and the emission lines.
This radiation can be divided into several terms (in CLOUDY words):
- incident: the one coming from the radiation source
- reflected: coming from the illuminated face of the cloud
- diffuse: generated within the cloud and diffuse out in every direction
- transmitted: the incident radiation that escapes the cloud

The most detailed studies on the ionization structure of gas clouds when exposed to external radiation are on the interstellar medium (ISM) of the Milky Way. But CLOUDY can be used (and it is used) also on external galaxies, including high-redshift galaxies, and active galactic nuclei (AGNs). 
The code is in continuous development, and in the last 10 years it received more than 200 citations per year.


## How to run CLOUDY

1. Prepare a clean folder “cloudytest”
2. Within it, create a “cloudytest.in” text file
3. Edit  “cloudytest.in” (see next section “The input file")
4. Run with path/to/source/cloudy.exe cloudytest.in
5. Wait…
6. Check the output files (within the same “cloudytest” directory)

### The input file

An input file is a simple text file with extension `.in`.
It contains informations about the radiation source and the exposed gas conditions, plus some computational important details (e.g., when to stop the simulation).

**Incident radiation**.
We need to specify intensity/luminosity + shape (i.e., SED).
CLOUDY distinguishes between
- intensity case: we specify the source intensity (in erg/s/cm^2)
- luminosity case: we specify the source luminosity (in erg/s) + we specify the cloud distance from the source (in cm)

**Clouds conditions**:
- gas density: we will see constant density (in cm^-3) simulations
- chemical composition: how much C/O? which metallicity? do we want dust?
- stopping criteria: when should the simulation stop?

A line-by-line analysis of the inoput file in the next section (“PDR and XDR files").


## PDR and XDR files

Within the `pdr` and `xdr` directories, I prepared two input files:
- PDR, i.e. photodissociation region: the radiation source is a star or a stellar population; in this case it is a synthetic spectrum for a continuous star-forming stellar population, made with the Starburst99 code. I set the source to emit G0=1000, where G0 is the adimensional flux between 6 and 13.6 eV (i.e. far UV) divided by 1.6e-3 erg/s/cm^2.
- XDR, i.e. X-ray dominated region: the radiation source is an X-ray emitter; in this case it is a synthetic spectrum for an AGN, available within CLOUDY. I set the source to emit 1 erg/s/cm^2 between 2 and 10 keV.

Apart from the radiation source, the conditions of the gas in the two simulations (PDR and XDR) are the same:
- `hden 4`: it means gas density = 1e4 cm^-3
- `abundances ISM`, `grains ISM`: ISM is a CLOUDY table of abundances and dust grains composition and size distribution, tailored for the Milky Way
- `metals and grains linear 1`: it means solar metallicity for both gas and dust
- `turbulence 1 km/s`: turbulence affects the shielding and pumping of lines, and it is extensively observed in the molecular ISM
- `cosmic ray background`: cosmic rays are important for molecular chemistry; this sets the value to the one observed in the Milky Way
- `no grain molecules` to avoid condensation of molecules onto grains
- `stop temperature off`: default would be to stop at a lower temperature of 4000 K
- `stop column 23`: column density NH=10^23 cm-2 to see a deep molecular region
- `set nend 5000`: nend = number of zones (default 1400)
- `iterate to convergence`: sets max=7 iterations for convergence of conservation equations (mass, energy, charge, ...)
- `save last` means only the last iteration

### Output files

We asked to save 4 files: `continuum`, `overview`, `pdr`, `lines cumulative emergent`. But CLOUDY always saves another file: the main output, called as the input but with the `.out` extension.

- **main output (.out)**: the only default output file, it gives a lot of informations about the run; it also lists warnings, failures, cautions, notes
- **continuum (.cont)**: intrinsic continuum to check the input SED, total continuum is transmitted + reflected; default units: Rydberg
- **overview (.ovr) and PDR (.pdr)**: first column of both is the cloud depth in cm; `ovr` contains temperature and ionization fractions, `pdr` contains column density and G0
- **lines cumulative emergent (.emis)**: first column is the cloud depth in cm, every other column is a specific line flux in erg/s/cm^2 (intensity case)


## The Jupyter Notebook

In the `notebooks` folder there is a jupyter notebook `cloudy_unibo_workshop.ipynb`. To use it I first suggest to create a python virtual environment (I chose to name it `cloudy_day`). To do so, choose a directory in your computer, open a terminal and execute the following commands line by line:

```
python3 -m venv cloudy_day
source cloudy_day/bin/activate
which pip  ### output should be path/to/cloudy_day/bin/pip 
pip install —upgrade pip
pip install jupyter ipython ipykernel
python -m ipykernel install —user —name=cloudy_day
pip install numpy pandas matplotlib seaborn scipy
```
