# SPECTROSCOPY_ASTRONOMY

## Setup and Installation

Follow these steps to set up the environment and install all necessary dependencies for processing and analyzing astronomical data.

### 1. Create a Virtual Environment

Open a terminal in the root directory of your project and run:

```bash
python3 -m venv .venv
```

(Note: On Debian/Ubuntu systems, if venv is missing, install it via sudo apt install python3-venv)

### 2. Activate the Virtual Environment
```bash
source .venv/bin/activate
```
### 3. Upgrade pip and Install Dependencies
```bash
pip install --upgrade pip
pip install astropy matplotlib numpy scipy pandas scikit-learn photutils specutils ccdproc astroquery jupyterlab
```
### 4. Verify installation
```bash
import astropy
print(f"Astropy version: {astropy.__version__}")
```
To deactivate the virtual environment when finished:
```bash
deactivate
```