# IRAF Setup & Data Reduction Guide
# IRAF Setup & Data Reduction Guide

## Repository Contents

This repository includes the following folders and files:

| File or folder | Description |
| :--- | :--- |
| `Cass/` | Directory containing the `Cass` dataset or working files. Its internal contents are not detailed in this repository overview. |
| `Cass_new/Cass/` | Additional directory containing a `Cass` folder, likely a separate or updated copy of the working dataset. |
| `ADP.2017-01-30T01_02_17.121.fits` | Astronomical data file in FITS format. FITS is commonly used to store astronomical images and associated data. |
| `ADP.2017-01-30T01_02_17.121.zip` | ZIP archive associated with the FITS data product; it can be extracted to inspect its contents. |
| `README.md` | Main documentation file. It explains the software setup and the data-reduction workflow described below. |
| `spec_gyak_01_eng.pdf` | English-language PDF practical/laboratory material, part 1. |
| `spec_gyak_02_eng.pdf` | English-language PDF practical/laboratory material, part 2. |
| `spec_gyak_03_eng.pdf` | English-language PDF practical/laboratory material, part 3. |
| `spec_gyak_04_eng.pdf` | English-language PDF practical/laboratory material, part 4. |
| `spec_gyak_normplot_eng.pdf` | English-language PDF material related to the `normplot` task or procedure. |

The descriptions of the PDF files are based on their filenames; consult each document for its exact contents. The two `Cass` directories are listed as they appear in the repository, without assuming what files they contain.

---

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

| `pwd` | **P**&#x72;int **W**&#x6F;rking **D**&#x69;rectory (shows current path) | `pwd` |

| `ls` | **L**&#x69;**s**&#x74; files and folders in current directory | `ls -la` |

| `cd` | **C**&#x68;ange **D**&#x69;rectory | `cd /path/to/folder` |

| `cd ..` | Move up one folder level | `cd ..` |

| `mkdir` | **M**&#x61;**k**&#x65; a new **dir**&#x65;ctory | `mkdir astronomy_data` |

| `cp` | **C**&#x6F;**p**&#x79; files or folders | `cp file1.fits file2.fits` |

| `mv` | **M**&#x6F;**v**&#x65; or rename files | `mv raw_image.fits /data/` |

| `rm` | **R**&#x65;**m**&#x6F;ve/delete a file | `rm temp.fits` |

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

**(Your terminal prompt will now show `(astro_env)` at the start of the line).**

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

   **(Select `xgterm` as your terminal type when prompted)**.

5. **Launch IRAF:**

```bash
xgterm &
```

   Inside the new XGTerm window, start IRAF:

```bash
cl
```

   **(or `irafcl` depending on your version)**.

---

## 5. IRAF Workflow & Slide Breakdown

Below is a detailed guide corresponding to each slide in the process.

---

### Slide 1: Installation of IRAF

![Slide 1 - IRAF Installation](https://raw.githubusercontent.com/placeholder/slide1.png)

- **Goal:** Set up IRAF inside a Linux or Ubuntu environment.

- **Commands:**

```bash
sudo apt get update
sudo apt install iraf xgterm
```

- **Steps:**

  1. Open Ubuntu / Terminal.

  2. Update package lists and install `iraf` and `xgterm`.

  3. Start the GUI terminal using `xgterm &`.

  4. Type `irafcl` inside the `xgterm` session to launch IRAF.

  5. If initializing for the first time, run `mkiraf` in your directory to generate setup files (`login.cl`).

---

### Slide 2: Listing the Header Info of FITS Files

![Slide 2 - Listing Header Info](https://raw.githubusercontent.com/placeholder/slide2.png)

- **Goal:** Create an image list and examine FITS file headers.

- **Workflow:**

  1. **Generate image list in Linux:**

```bash
ls *fits > all.list
```

     Creates a text file named `all.list` containing all `.fits` file names in the working directory.

  2. **Navigate to IRAF Task:**

```text
images => util => epar imhead
```

- **Parameter Settings (`epar imhead`):**

  - `images = @all.list`: Input file list.

  - `(imlist = *fits)`: Target file extension match.

- **Controls:**

  - Execute task: `:g` (GO)

  - Exit editor without running: `:q` (QUIT)

---

### Slide 3: Bias Correction - `zerocombine` (Part 1)

![Slide 3 - Zerocombine Part 1](https://raw.githubusercontent.com/placeholder/slide3.png)

- **Goal:** Combine multiple bias/zero calibration frames into a single master bias image.

- **Workflow:**

```text
noao => imred => ccdred => epar zerocombine
```

- **Parameter Settings:**

  - `input = @fits.list`: List containing input raw bias FITS files.

  - `output = Zero`: Name of the combined master bias image output.

  - `combine = average`: Combination algorithm (mean value per pixel).

  - `reject = minmax`: Rejection algorithm to eliminate cosmic rays.

  - `ccdtype = zero`: Image type designated for bias frames.

  - `process = no`: Disables preliminary calibration during combination.

  - `delete = no`: Keeps individual input bias files after combining.

  - `rdnoise = 0.`: Specified CCD readout noise (in electrons).

---

### Slide 4: Bias Correction - `zerocombine` (Part 2)

![Slide 4 - Zerocombine Part 2](https://raw.githubusercontent.com/placeholder/slide4.png)

- **Goal:** Fine-tune noise and clipping specifications for `zerocombine`.

- **Parameter Settings:**

  - `gain = 1.`: CCD gain factor (electrons / Digital Number).

  - `snoise = 0.`: Sensitivity noise fraction.

  - `pclip = -0.5`: Percentile clipping threshold parameter.

  - `blank = 0.`: Default value for pixels with no data.

- **Controls:**

  - Execute combining process: `:g`

  - Exit without saving parameters: `:q`

---

### Slide 5: Bias Correction - `ccdproc` (Part 1 - Processing Steps)

![Slide 5 - ccdproc Part 1](https://raw.githubusercontent.com/placeholder/slide5.png)

- **Goal:** Configure basic calibration processing routines using `ccdproc`.

- **Workflow:**

```text
noao => imred => ccdred => epar ccdproc
```

- **Parameter Settings:**

  - `images = @fits.list`: List of science or flat images to calibrate.

  - `ccdtype = flat` (or `object`): Specifies frame type to process.

  - `fixpix = no`: Disables bad column/pixel masking.

  - `overscan = no`: Disables overscan strip subtraction.

  - `zerocor = yes`: **Enables master bias (zero-level) subtraction.**

  - `darkcor = no`: Disables dark frame subtraction.

  - `flatcor = no`: Disables flat-field correction at this stage.

---

### Slide 6: Bias Correction - `ccdproc` (Part 2 - Calibration Files)

![Slide 6 - ccdproc Part 2](https://raw.githubusercontent.com/placeholder/slide6.png)

- **Goal:** Link calibration reference images and set readout orientation.

- **Parameter Settings:**

  - `readaxis = column`: Defines CCD readout orientation axis (column or line).

  - `zero = Zero`: Specifies master bias image (`Zero.fits`) generated by `zerocombine`.

  - `interactive = no`: Runs processing automatically without interactive fitting prompts.

  - `function = legendre`: Overscan fitting polynomial function model.

  - `order = 1`: Polynomial order for fitting overscan.

---

### Slide 7: Bias Correction - `ccdproc` (Part 3 - Rejection Settings)

![Slide 7 - ccdproc Part 3](https://raw.githubusercontent.com/placeholder/slide7.png)

- **Goal:** Configure statistical clipping limits for artifact rejection during reduction.

- **Parameter Settings:**

  - `naverag = 1`: Number of sample points combined per step.

  - `niterat = 1`: Rejection iterations for fitting algorithms.

  - `low_rej = 3.`: Lower sigma rejection threshold (3 $\sigma$).

  - `high_re = 3.`: Upper sigma rejection threshold (3 $\sigma$).

- **Execution:** Run with `:g` to execute processing across selected files.

# Spectroscopic Data Reduction with IRAF

## Slide 8: Flat-Field Correction - flatcombine (Part 1)

- **Goal:** Combine multiple flat-field (Tungsten lamp) images into a single master flat image to correct pixel-to-pixel sensitivity variations.

- **Workflow:**

```text
noao => imred => ccdred => epar flatcombine
```

- **Parameter Settings:**

  - `input = @fits.list` (e.g., `flat.list`): List of flat-field FITS files to combine.

  - `output = Flat`: Output master flat root name (`Flat.fits`).

  - `combine = median`: Combination algorithm (median value per pixel).

  - `reject = avsigclip`: Rejection algorithm for cosmic rays and outliers.

  - `ccdtype = flat`: CCD image type designated for flat frames.

  - `process = no`: Disables preliminary calibration during combination.

  - `subsets = no`: Disables grouping by filter subsets.

  - `delete = no`: Keeps individual input flat files after combining.

  - `scale = mean`: Scales frames by their mean intensity before combining.

  - `nlow = 1 / nhigh = 1`: Number of low/high pixels to reject.

  - `lsigma = 3. / hsigma = 3.`: Lower and upper sigma clipping thresholds.

## Slide 9: Flat-Field Correction - flatcombine (Part 2)

- **Goal:** Configure detector noise parameters for `flatcombine`.

- **Parameter Settings:**

  - `rdnoise = 0.`: CCD readout noise (in electrons).

  - `gain = 1.`: CCD gain factor (electrons / DN).

  - `snoise = 0.`: Sensitivity noise fraction.

  - `pclip = -0.5`: Percentile clipping parameter.

  - `blank = 1.`: Default pixel value if no data is present.

- **Controls:**

  - Execute combining process: `:g`

  - Exit without running: `:q`

## Slide 10: Flat-Field Correction - Identify Usable Region (implot)

- **Goal:** Inspect the spatial cross-section of the master flat to determine the illuminated slit boundaries (excluding unilluminated edges).

- **Command:**

```bash
implot Flat.fits
```

- **Observation:**

  - Plot shows intensity across columns (1 to 50) at line 512.

  - The usable illuminated slit region lies between columns 12 and 42 (`[12:42,2:1024]`).

  - Press `q` inside the graphics window to exit `implot`.

## Slide 11: Flat-Field Correction & Trimming - ccdproc (Part 1)

- **Goal:** Trim the science frames to the usable slit area and apply flat-field division.

- **Workflow:**

```text
noao => imred => ccdred => epar ccdproc
```

- **Parameter Settings:**

  - `images = @fits.list`: List of science images to correct.

  - `ccdtype = object`: Specifies science object frames.

  - `trim = yes`: Enables image trimming.

  - `zerocor = no`: Bias correction already applied in previous step.

  - `darkcor = no`: Disables dark subtraction.

  - `flatcor = yes`: Enables flat-field correction.

## Slide 12: Flat-Field Correction & Trimming - ccdproc (Part 2)

- **Goal:** Specify the exact trim section and link the master flat image.

- **Parameter Settings:**

  - `readaxi = column`: Readout axis orientation.

  - `trimsec = [12:42,2:1024]`: Data section to keep (removes unilluminated edges).

  - `flat = Flat`: Specifies master flat calibration image (`Flat.fits`).

- **Execution:** Run with `:g` to trim and flat-field correct all target science spectra. (Note: Also trim comparison lamp frames with the exact same `trimsec` so spatial coordinates match).

## Slide 13: Aperture Extraction - apall (Object Spectrum - Part 1)

- **Goal:** Define, trace, subtract sky background, and extract the 1D spectrum of the target star first.

- **Workflow:**

```text
noao => twodspec => apextract => epar apall
```

- **Parameter Settings:**

  - `input = obj`: Input 2D science spectrum.

  - `output = obj.ap`: Output extracted 1D spectrum (`obj.ap.0001.fits`).

  - `apertur = 1`: Number of apertures to extract.

  - `format = onedspec`: Extracted 1D spectrum format.

  - `referen =` (blank): No reference image needed when extracting the primary object.

  - `interac = yes, find = yes, recente = yes, resize = yes, edit = yes, trace = yes, fittrac = yes, extract = yes`: Enable all interactive steps.

  - `extras = no`: Do not extract extra sky/sigma bands.

## Slide 14: Aperture Extraction - apall (Object Spectrum - Part 2: Background)

- **Goal:** Configure aperture limits and sky background subtraction regions.

- **Parameter Settings:**

  - `review = yes`: Review extracted 1D spectrum interactively after extraction.

  - `line = INDEF / nsum = 10`: Sum 10 dispersion lines around the middle to find the peak.

  - `lower = -5. / upper = 5.`: Default aperture limits relative to center.

  - `b_funct = chebyshev / b_order = 1`: Background fitting function and order.

  - `b_sampl = -10:-6, 6:10`: Background sky regions on both sides of the star peak.

  - `b_naver = -3`: Median across every 3 background pixels.

## Slide 15: Aperture Extraction - apall (Object Spectrum - Part 3: Centering & Tracing)

- **Goal:** Configure peak centering, trace fitting function, and extraction mode.

- **Parameter Settings:**

  - `nfind = 1`: Number of apertures to find automatically.

  - `t_nsum = 10 / t_step = 10`: Tracing step and summed lines along the dispersion axis.

  - `t_funct = spline3 / t_order = 1`: Function (`spline3`) and order used to fit the aperture trace.

  - `backgro = fit`: Subtract fitted sky background during extraction.

  - `weights = none`: Extraction weighting (none or variance).

- **Execution:** Run with `:g` and answer yes to interactive prompts.

## Slide 16: Interactive Aperture Definition (apall)

- **Goal:** Verify or adjust the aperture boundaries around the stellar spatial profile.

- **Interactive Keyboard Commands:**

  - `d`: Delete current aperture.

  - `n`: Define a new aperture at cursor position.

  - `l`: Set left (lower) edge of the aperture at cursor position.

  - `u`: Set right (upper) edge of the aperture at cursor position.

  - `c`: Re-center aperture on the peak.

  - `b`: Enter background definition mode.

  - `q`: Accept aperture and proceed to tracing.

## Slide 17: Interactive Background Definition (apall)

- **Goal:** Select clean sky regions on either side of the stellar profile to remove sky emission.

- **Interactive Keyboard Commands:**

  - `t`: Delete existing background sample regions (törlés).

  - `s` (twice): Mark start and end of a new background sample region (kijelölés).

  - `f`: Fit the background function (illlesztés) to the selected regions.

  - `q`: Quit background editor and return to aperture editor.

## Slide 18: Interactive Aperture Tracing (apall)

- **Goal:** Fit a smooth curve to the centroid position of the star along all rows of the CCD.

- **Interactive Commands:**

  - `:order n`: Change polynomial/spline order (e.g., `:order 2`).

  - `:function legendre`: Switch fitting function (e.g., `legendre` or `spline3`).

  - `:niter 2`: Set sigma-clipping iterations to reject outlier trace points.

  - `f`: Recompute and plot the updated fit.

  - `q`: Accept the trace (RMS \~ 0.001–0.002 pixels) and save to `database/`.

## Slide 19: Reviewing the Extracted Object Spectrum

- **Goal:** Inspect the resulting 1D spectrum before wavelength calibration.

- **Observation:**

  - The X-axis is still in pixels (0 to 1023), and the Y-axis shows counts.

  - Strong stellar absorption lines (such as Hα near pixel \~740–760) are clearly visible.

  - Press `q` to close the review window and save `obj.ap.0001.fits`.

## Slide 20: Extracting the Comparison Lamp Apertures (apall)

- **Goal:** Extract the 1D spectra of the FeAr calibration lamps using the exact spatial trace of the target star.

- **Workflow:**

```text
noao => twodspec => apextract => epar apall
```

- **Parameter Settings:**

  - `input = fear1,fear2`: Comparison lamp 2D spectra (taken before and after the star).

  - `output = fear1.ap,fear2.ap`: Output extracted 1D lamp spectra.

  - `apertur = 1 / format = onedspec`

  - `referen = obj`: Uses the saved aperture trace of the object (`database/apobj`).

  - `interac = yes, extract = yes`

  - `find = no, recente = no, resize = no, edit = no, trace = no, fittrac = no, extras = no`: Disable all finding and tracing (lamps fill the entire slit).

  - `backgro = none`: Disable background subtraction (crucial so emission lines are not subtracted from themselves).

## Slide 21: Reviewing the Extracted Lamp Spectrum

- **Goal:** Verify that the extracted FeAr lamp shows sharp, positive emission peaks.

- **Observation:**

  - Positive emission peaks extend upward from 0 to \~65,000 counts across pixels 0 to 1023.

  - Press `q` to save `fear1.ap.0001.fits` and `fear2.ap.0001.fits`.

## Slide 22: Wavelength Calibration - identify (Setup)

- **Goal:** Map pixel positions to known laboratory wavelengths (in Angstroms) on the first comparison lamp (`fear1.ap.0001`).

- **Workflow:**

```text
noao => onedspec => epar identify
```

- **Parameter Settings:**

  - `images = fear1.ap.0001`: First extracted lamp spectrum.

  - `databas = database`: Directory where the wavelength solution will be stored.

  - `coordli = linelists$fear.dat` (or `linelists$idhenear.dat`): Reference line catalog.

  - `ftype = emission`: Feature type to center on.

  - `fwidth = 4. / cradius = 5.`: Feature width and centering radius in pixels.

  - `functio = legendre / order = 3`: Dispersion fitting function and polynomial order.

## Slide 23: Interactive Line Identification (identify)

- **Goal:** Manually identify 4–5 strong, well-spaced emission lines using a reference spectrum map (`FeAr6600.ps`), then auto-identify the remaining lines and fit the dispersion curve.

- **Interactive Keyboard Commands:**

  - `m`: Mark the emission peak under the cursor, type its exact wavelength in Å (e.g., `6604.8534`), and press Enter.

  - `d`: Delete an incorrect or `INDEF` line identification.

  - `l`: Automatically identify remaining lines from `coordli` once initial lines are marked.

  - `f`: Fit the dispersion solution (`legendre`, `order = 3`) and inspect residuals (RMS < 0.01 Å).

  - `:order n / :function legendre`: Adjust polynomial order or function inside the fit view.

  - `q`: Return from fit view, and press `q` again to quit and answer yes to save to database (`database/idfear1.ap.0001`).

## Slide 24: Propagating Wavelength Solution - reidentify

- **Goal:** Automatically transfer and refit the wavelength solution from `fear1.ap.0001` onto the second comparison lamp (`fear2.ap.0001`).

- **Workflow:**

```text
noao => onedspec => epar reidentify
```

- **Parameter Settings:**

  - `referenc = fear1.ap.0001`: Calibrated reference lamp spectrum.

  - `images = fear2.ap.0001`: Second (or remaining) lamp spectrum to calibrate.

  - `interac = yes`: Allows interactive inspection of the refit.

  - `refit = yes`: Refits the coordinate dispersion function to account for spectrograph flexure.

  - `coordli = linelists$fear.dat`: Coordinate line list.

  - `databas = database`: Database directory.

- **Execution:** Run with `:g`, confirm interactive fitting (yes), press `q` to accept, and save to database.

## Slide 25: Linking Reference Lamps to Object - refspectra

- **Goal:** Write the `REFSPEC1` and `REFSPEC2` keywords into the science spectrum FITS header so IRAF knows which calibrated lamps bracket the observation.

- **Workflow:**

```text
noao => onedspec => epar refspectra
```

- **Parameter Settings:**

  - `input = obj.ap.0001`: Extracted 1D science spectrum.

  - `referen = fear1.ap.0001,fear2.ap.0001`: Bracketing calibrated lamp spectra.

  - `ignorea = yes`: Ignore aperture numbers if needed.

  - `select = average` (or `interp` if using time-weighted interpolation): Method to combine reference solutions.

  - `confirm = yes / assign = yes`: Confirm and write header assignments.

- **Execution:** Run with `:g` and confirm with `yes`.

## Slide 26: Applying Dispersion Correction - dispcor

- **Goal:** Resample and linearize the science spectrum onto a uniform wavelength scale in Angstroms.

- **Workflow:**

```text
noao => onedspec => epar dispcor
```

- **Parameter Settings:**

  - `input = obj.ap.0001`: Input uncalibrated 1D science spectrum.

  - `output = obj.kal`: Output wavelength-calibrated spectrum (`obj.kal.fits`).

  - `lineari = yes`: Linearize (interpolate) spectrum onto a constant Δλ grid.

  - `databas = database`: Dispersion solution database.

  - `w1 = INDEF, w2 = INDEF, dw = INDEF, nw = INDEF`: Automatically determine wavelength limits and step size.

  - `flux = yes`: Conserve total flux during resampling.

- **Execution:** Run with `:g` (or directly via `dispcor obj.ap.0001 obj.kal`).

## Slide 27: Inspecting the Wavelength-Calibrated Spectrum (splot)

- **Goal:** Verify that the X-axis is now accurately calibrated in Angstroms.

- **Command:**

```bash
splot obj.kal.fits
```

- **Observation:**

  - The X-axis displays wavelength (angstroms) (\~6510 to 6710 Å), with the Hα absorption line centered near 6562.8 Å.

## Slide 28: Continuum Normalization - continuum (Setup)

- **Goal:** Fit a smooth curve to the stellar continuum and divide the spectrum by that fit so the continuum level equals 1.0 across all wavelengths (essential for equivalent width and abundance measurements).

- **Workflow:**

```text
noao => onedspec => epar continuum
```

- **Parameter Settings:**

  - `input = obj.kal`: Wavelength-calibrated spectrum.

  - `output = obj.nor`: Final normalized spectrum (`obj.nor.fits`).

  - `type = ratio`: Divide input spectrum by the fitted continuum.

  - `wavesca = yes`: Use wavelength scale on X-axis.

  - `interac = yes`: Fit continuum interactively.

  - `functio = spline3 / order = 1`: Fitting function and order.

  - `low_rej = 2. / high_re = 0.`: Asymmetric sigma clipping (`low_rej = 2` rejects narrow absorption lines downward while `high_re = 0` keeps upper continuum points).

  - `niterat = 10 / grow = 1.`: Rejection iterations and growing radius.

## Slide 29: Interactive Continuum Fitting (continuum)

- **Goal:** Exclude broad absorption features (such as the wide wings of Hα) from the fit so they do not pull the continuum curve downward.

- **Interactive Keyboard Commands:**

  - `s` (twice, i.e., 2xs): Mark clean continuum sample intervals on the left and right of broad lines (e.g., 6510–6550 Å and 6580–6710 Å), leaving Hα excluded.

  - `f`: Recompute the continuum fit over the selected intervals.

  - `:order n`: Adjust spline/polynomial order if needed.

  - `q`: Accept the fit and write the normalized output file (`obj.nor.fits`).

## Slide 30: The Final Reduced Spectrum!

- **Goal:** Inspect the fully reduced, wavelength-calibrated, and continuum-normalized stellar spectrum (`obj.nor.fits`).

- **Command:**

```bash
splot obj.nor.fits
```

- **Result:**

  - **X-axis:** Linearized wavelength (angstroms) (\~6510–6710 Å).

  - **Y-axis:** Normalized relative flux with the continuum flat at 1.0 and absorption lines extending downward between 0 and 1.

  - The spectrum is now ready for scientific analysis (radial velocity measurement via cross-correlation, equivalent widths, and stellar abundance analysis).
