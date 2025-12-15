# Ginan Scripts

This directory contains a number of useful scripts that facilitate:  

  - running Ginan via:
    - a graphical user interface (under `scripts/GinanUI`) 
    - shell scripts for installing Ginan natively (under `scripts/installation`)
    - scripts that handle downloading necessary input files (`auto_download_PPP.py`)
  - plotting Ginan output files, including
    - POS files (`plot_pos.py`)
    - ZTD files (`plotting/ztd_plot.py`)
  - exploring and debugging Ginan and it's Kalman filter via:
    - The Ginan Exploratory Data Analysis (EDA) tool (`scripts/GinanEDA`)

Each sub-directory listed above contains it's own README, which provides further details on running the various functionalities. 
The rest of this README will cover the files located on the `scripts` directory, namely:
1. `auto_download_PPP.py`
2. `plot_pos.py`
3. `plot_trace_res.py `
4. `s3_filehandler.py`

## _**Recommended:**_  
Before continuing, it is highly recommended that you create a python virtual environment if you have not already done so as suggested on the root README file:
```bash
# Create virtual environment
python3 -m venv ginan-env
source ginan-env/bin/activate
```
The above line will the virtual environment in your current working directory. Once the above is complete, you will have the virtual environment in your current working directory. 

You can then install all python dependencies via a `pip` command:
```bash
# Install Python dependencies
pip3 install -r requirements.txt
```
## 1. auto_download_PPP
The `auto_download_PPP.py` script makes it easier to download the necessary high precision products and model files necessary for processing RINEX data in Ginan to produce PPP results.

Based on a few details provided by the user via arguments in the command-line interface (CLI), the script fetches the appropriate files for a given date or date range. These files includes products such as:

  - precise orbits (`.SP3`) 
  - broadcast orbits (`BRDC.RNX`)
  - precise clocks (`.CLK`) 
  - Earth rotation parameters (`.ERP` or IERS IAU2000 file)
  - CORS station positions and metadata (`.SNX`), 
  - satellite metadata (`.SNX`)
  - code biases (`.BIA`) 

The product files are mostly obtained from the NASA archive known as the Crustal Dynamics Data Information System (CDDIS). This is one of NASA's Distributed Active Archive Centers (DAACs).

To use and download from this archive, you will need to create an Earthdata Login account and provide your username and password in a `.netrc` file. This outlined below in Section 1.2.

It also includes the various model files needed:

  - planetary ephemerides (JPL Development Ephemeris `DE436.1950.2050`),
  - atmospheric loading,
  - geopotential (Earth Gravitational Model `EGM2008`), 
  - geomagnetic reference field (International Geomagnetic Reference Field `IGRF14`)  
  - ocean loading, 
  - ocean pole tide coefficients,
  - ocean tide potential (Finite Element Solution 2014b `FES2014b`),
  - troposphere (Global Pressure and Temperature model `GPT2.5`)

These are needed for running PPP. 

### 1.1 Earthdata Login Credentials - CDDIS Downloads
To download product files from the Crustal Dynamics Data Information System (CDDIS) web archive you will need an Earthdata Login account credentials saved to your machine.

#### 1.1.1 Create New Earthdata Login Account (if you don't have one):
You can create a new Earthdata account at the following website:
https://urs.earthdata.nasa.gov/users/new

#### 1.1.2 Save Credentials to Your Machine

Once you have your username and password, these must be saved in a `.netrc` file on your home directory. Depending on your operating system, this can be achieved in different ways:

##### Unix / Linux / MacOS:
This can be done in a terminal window via:
```bash
echo "machine urs.earthdata.nasa.gov login your_username password your_password" >> ~/.netrc
```
Make sure to set appropriately restrictive file permissions as well (read / write by the current user only):
```bash
chmod 0600 ~/.netrc
```

##### Windows:
1. Open Notepad or any plain-text editor.

2. Enter the `.netrc` format shown above, replace the placeholders with your actual login and password.

3. Save the file as `_netrc` (with an underscore instead of a period) in your home directory.

4. Set file permissions:
    - Right-click _netrc file and choose Properties
    - Go to the Security tab → Click Edit
    - Remove access for all other users except your own account
    - Click Apply to save the changes.

The above Earthdata credential instructions are adapted from the following website:<br>
https://nsidc.org/data/user-resources/help-center/creating-netrc-file-earthdata-login

### 1.2 Test "auto_download_PPP" in Virtual Enviroment
Once you have your credentials set up, you are ready to automatically download all necessary product and model files via python. Before we do this though, we will test that the script is working as expected.

First, make sure you have your virtual environment activated in your current terminal. Following the way we recommended to create the environment above, this would look like:
```bash
# Activate virtual environment - ginan-env
source ginan-env/bin/activate
```

Next, test that the `auto_download_PPP` script functions correctly:
```bash
# Test auto_download_PPP script:
python auto_download_PPP.py --help
```
This will display the help page with detailed information on all possible arugments into the function itself.

### 1.3 Example Run of "auto_download_PPP"
With your virtual environment active, you can now download the product files needed for a PPP run in Ginan.

We will use the `igs-station` preset to download RINEX files for two IGS stations for two days in 2024 together with all the product and model files needed to run this in Ginan. 

```bash
# Example run of auto_download_PPP:
python auto_download_PPP.py --target-dir /data/temp/products --rinex-data-dir /data/temp/data --station-list ALIC,HOB2 --start-datetime 2024-01-06_00:00:00 --end-datetime 2024-01-07_23:59:30 --preset=igs-station --dont-replace
```
Each of the arguments used above are described below:

- `--target-dir`: sets the directory where product files are downloaded into (with model files going into `target-dir/tables`)
- `--rinex-data-dir`: sets the directory where observational RINEX files are downloaded into (for `ALIC` and `HOB2` in this case)
- `--station-list`: this list of stations to download RINEX files for
- `--start-datetime`: the start date and time to download files from
- `--end-datetime`: the end date and time to download files to
- `--preset=igs-station`: tells the script to download all necessary products to run this in PPP mode in Ginan: `SP3`,`CLK`, `BRDC`, `SNX`, `BIA`, satellite metadata `SNX`, IERS Earth orientation data, plus all necessary model files in `products/tables`

If you are aware of which files you need to download, you can use the default `preset` mode of `manual` and just choose the various files needed based on their flags. For detailed info on all possible flags, run:
```bash
python auto_download_PPP.py --help
```

## 2. plot_pos

## 3. plot_trace_res

## 4. s3_filehandler