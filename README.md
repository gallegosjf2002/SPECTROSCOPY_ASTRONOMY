# IRAF Setup & Data Reduction Guide

This repository contains a step-by-step guide for setting up an astronomical data reduction environment on Linux (or WSL) and executing basic IRAF pipeline tasks such as header inspection, bias frame combination, and image calibration.

---

## Table of Contents
1. [Essential Linux Terminal Commands](#1-essential-linux-terminal-commands)
2. [Installing SAOImage DS9 on Linux](#2-installing-saoimage-ds9-on-linux)
3. [Python Virtual Environment (`venv`) Guide](#3-python-virtual-environment-venv-guide)
4. [IRAF Setup Instructions](#4-iraf-setup-instructions)
5. [IRAF Workflow & Slide Breakdown](#5-iraf-workflow--slide-breakdown)

---

## 1. Essential Linux Terminal Commands

Here are fundamental Linux terminal commands needed to navigate directories and manage files:

| Command | Description | Example |
| :--- | :--- | :--- |
| `pwd` | **P**rint **W**orking **D**irectory (shows current path) | `pwd` |
| `ls` | **L**i**s**t files and folders in current directory | `ls -la` |
| `cd` | **C**hange **D**irectory | `cd /path/to/folder` |
| `cd ..` | Move up one folder level | `cd ..` |
| `mkdir` | **M**a**k**e a new **dir**ectory | `mkdir astronomy_data` |
| `cp` | **C**o**p**y files or folders | `cp file1.fits file2.fits` |
| `mv` | **M**o**v**e or rename files | `mv raw_image.fits /data/` |
| `rm` | **R**e**m**ove/delete a file | `rm temp.fits` |

---

## 2. Installing SAOImage DS9 on Linux

**SAOImage DS9** (commonly called DS9) is an astronomical imaging and data visualization application.

### Installation via APT (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install saods9
```

### Manual Installation (Binary Tarball)
If DS9 is not available in your repository or you want the latest standalone version:

```bash
# 1. Download DS9 tarball
wget https://ds9.si.edu/download/ubuntu22/ds9.ubuntu22.tar.gz

# 2. Extract the archive
tar -xvf ds9.ubuntu22.tar.gz

# 3. Move the binary to standard system PATH
sudo mv ds9 /usr/local/bin/

# 4. Verify installation
ds9
```

---

## 3. Python Virtual Environment (`venv`) Guide

Using a virtual environment isolates your Python dependencies for astronomical packages (like `astropy`, `pyraf`, etc.).

### Step 1: Install `python3-venv` Package
```bash
sudo apt update
sudo apt install python3-venv
```

### Step 2: Create a Virtual Environment
Navigate to your project directory and create an environment named `astro_env`:
```bash
python3 -m venv astro_env
```

### Step 3: Activate the Virtual Environment
```bash
source astro_env/bin/activate
```
*(Your terminal prompt will now show `(astro_env)` at the start of the line).*

### Step 4: Deactivate the Virtual Environment
When finished working:
```bash
deactivate
```

---

## 4. IRAF Setup Instructions

Follow these steps to set up **IRAF** on Ubuntu / Linux / WSL (Windows Subsystem for Linux):

1. **Enable WSL (Windows users only):** Refer to [Microsoft WSL Installation Guide](https://learn.microsoft.com/en-us/windows/wsl/install).
2. **Update repositories:**
   ```bash
   sudo apt get update
   ```
3. **Install IRAF and XGTerm:**
   ```bash
   sudo apt install iraf xgterm
   ```
4. **Initialize IRAF environment (First time only):**
   ```bash
   mkdiraf
   ```
   *(Select `xgterm` as your terminal type when prompted)*.
5. **Launch IRAF:**
   ```bash
   xgterm &
   ```
   Inside the new XGTerm window, start IRAF:
   ```bash
   cl
   ```
   *(or `irafcl` depending on your version)*.

---

## 5. IRAF Workflow & Slide Breakdown

Below is a detailed guide corresponding to each slide in the process.

---

### Slide 1: Installation of IRAF
![Slide 1 - IRAF Installation](https://raw.githubusercontent.com/placeholder/slide1.png)

* **Goal:** Set up IRAF inside a Linux or Ubuntu environment.
* **Commands:**
  ```bash
  sudo apt get update
  sudo apt install iraf xgterm
  ```
* **Steps:**
  1. Open Ubuntu / Terminal.
  2. Update package lists and install `iraf` and `xgterm`.
  3. Start the GUI terminal using `xgterm &`.
  4. Type `irafcl` inside the `xgterm` session to launch IRAF.
  5. If initializing for the first time, run `mkiraf` in your directory to generate setup files (`login.cl`).

---

### Slide 2: Listing the Header Info of FITS Files
![Slide 2 - Listing Header Info](https://raw.githubusercontent.com/placeholder/slide2.png)

* **Goal:** Create an image list and examine FITS file headers.
* **Workflow:**
  1. **Generate image list in Linux:**
     ```bash
     ls *fits > all.list
     ```
     Creates a text file named `all.list` containing all `.fits` file names in the working directory.
  2. **Navigate to IRAF Task:**
     ```text
     images => util => epar imhead
     ```
* **Parameter Settings (`epar imhead`):**
  * `images = @all.list`: Input file list.
  * `(imlist = *fits)`: Target file extension match.
* **Controls:**
  * Execute task: `:g` (GO)
  * Exit editor without running: `:q` (QUIT)

---

### Slide 3: Bias Correction - `zerocombine` (Part 1)
![Slide 3 - Zerocombine Part 1](https://raw.githubusercontent.com/placeholder/slide3.png)

* **Goal:** Combine multiple bias/zero calibration frames into a single master bias image.
* **Workflow:**
  ```text
  noao => imred => ccdred => epar zerocombine
  ```
* **Parameter Settings:**
  * `input = @fits.list`: List containing input raw bias FITS files.
  * `output = Zero`: Name of the combined master bias image output.
  * `combine = average`: Combination algorithm (mean value per pixel).
  * `reject = minmax`: Rejection algorithm to eliminate cosmic rays.
  * `ccdtype = zero`: Image type designated for bias frames.
  * `process = no`: Disables preliminary calibration during combination.
  * `delete = no`: Keeps individual input bias files after combining.
  * `rdnoise = 0.`: Specified CCD readout noise (in electrons).

---

### Slide 4: Bias Correction - `zerocombine` (Part 2)
![Slide 4 - Zerocombine Part 2](https://raw.githubusercontent.com/placeholder/slide4.png)

* **Goal:** Fine-tune noise and clipping specifications for `zerocombine`.
* **Parameter Settings:**
  * `gain = 1.`: CCD gain factor (electrons / Digital Number).
  * `snoise = 0.`: Sensitivity noise fraction.
  * `pclip = -0.5`: Percentile clipping threshold parameter.
  * `blank = 0.`: Default value for pixels with no data.
* **Controls:**
  * Execute combining process: `:g`
  * Exit without saving parameters: `:q`

---

### Slide 5: Bias Correction - `ccdproc` (Part 1 - Processing Steps)
![Slide 5 - ccdproc Part 1](https://raw.githubusercontent.com/placeholder/slide5.png)

* **Goal:** Configure basic calibration processing routines using `ccdproc`.
* **Workflow:**
  ```text
  noao => imred => ccdred => epar ccdproc
  ```
* **Parameter Settings:**
  * `images = @fits.list`: List of science or flat images to calibrate.
  * `ccdtype = flat` (or `object`): Specifies frame type to process.
  * `fixpix = no`: Disables bad column/pixel masking.
  * `overscan = no`: Disables overscan strip subtraction.
  * `zerocor = yes`: **Enables master bias (zero-level) subtraction.**
  * `darkcor = no`: Disables dark frame subtraction.
  * `flatcor = no`: Disables flat-field correction at this stage.

---

### Slide 6: Bias Correction - `ccdproc` (Part 2 - Calibration Files)
![Slide 6 - ccdproc Part 2](https://raw.githubusercontent.com/placeholder/slide6.png)

* **Goal:** Link calibration reference images and set readout orientation.
* **Parameter Settings:**
  * `readaxis = column`: Defines CCD readout orientation axis (column or line).
  * `zero = Zero`: Specifies master bias image (`Zero.fits`) generated by `zerocombine`.
  * `interactive = no`: Runs processing automatically without interactive fitting prompts.
  * `function = legendre`: Overscan fitting polynomial function model.
  * `order = 1`: Polynomial order for fitting overscan.

---

### Slide 7: Bias Correction - `ccdproc` (Part 3 - Rejection Settings)
![Slide 7 - ccdproc Part 3](https://raw.githubusercontent.com/placeholder/slide7.png)

* **Goal:** Configure statistical clipping limits for artifact rejection during reduction.
* **Parameter Settings:**
  * `naverag = 1`: Number of sample points combined per step.
  * `niterat = 1`: Rejection iterations for fitting algorithms.
  * `low_rej = 3.`: Lower sigma rejection threshold (3 $\sigma$).
  * `high_re = 3.`: Upper sigma rejection threshold (3 $\sigma$).
* **Execution:** Run with `:g` to execute processing across selected files.