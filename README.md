# EMTG_153 Instructions
The following instructions are for setting up NASA's EMTG on Mac. This repository is a forked version of the original EMTG repository and has been edited to ensure it works on Mac, assuming the rest is setup correctly as shown below. Changes that were made between this repository and the original repository may be viewed in the code itself, but details of the changes are also outlined at the end of this README. Note that these instructions confirm to work on the new Mac M1/M2 processors, but might still work on Intel processors with little to no changes.

## Prerequisites
Below are things that need to be done before EMTG can be built. It is recommended to use Homebrew to install most necessary software.

### SPICE (no homebrew needed)
Choose appropiate OS and follow instructions (https://naif.jpl.nasa.gov/naif/toolkit_C.html)

### GSL
Install GSL (`brew install gsl`) and make sure the `EMTG-Config.cmake' file points to its `lib` directory

### CMake
Install CMake (`brew install cmake`)

### Boost
Install Boost if not already dont so (`brew install boost`)

### GCC version 11
1. Install GCC version 11 (`brew install gcc@11`)
2. In terminal shell profile (if using bash then `nano ~/.bashrc`, if using zshrc then `nano ~/.zshrc`), add pointers to gcc
    1. `export CXX=/path/to/g++-11` (for example, `export CXX=/opt/homebrew/Cellar/gcc@11/11.3.0/bin/g++-11`)
    2. `export CC=/path/to/gcc-11` (for example, `export CC=/opt/homebrew/Cellar/gcc@11/11.3.0/bin/gcc-11`)
    3. `export FC=/path/to/gfortran-11` (for example, `export FC=/opt/homebrew/Cellar/gcc@11/11.3.0/bin/gfortran-11`)
4. Make sure shell is updated by either quitting and reopening or running `source ~/.bashrc' (or `source ~/.zshrc`)

### Autoconf
Install autoconf (`brew install autoconf`)

### SNOPT (version 7, may or may not work with other versions)
1. Unzip the zip flolder containing SNOPT anywhere
2. Go inside SNOPT root
3. Run `./configure --with-cpp`
4. Run `make` (warnings can be ignored)
5. Run `make install`
6. Run `make check` and ensure no errors occur
7. Inside SNOPT root, run `git clone https://github.com/snopt/snopt-interface.git` and rename new folder (should be `/snopt-interface') to `/interfaces` (with `mv snopt-interface interfaces`)
8. Go inside `/interfaces`
9. Make sure autoconf is installed (homebrew)
10. Run `autoconf`, run `autoupdate` if prompted then rerun `autoconf`
11. Run `./configure --with-snopt=/path/to/your/snopt/lib` (for example, `./configure --with-snopt=/Users/nick/Documents/SNOPT/snopt7/lib`)
12. Run `make` (ignore warnings)
13. Run `make install`
14. Run `make examples`

## Building EMTG_153
1. Clone EMTG_153 (`git clone https://github.com/RNakano1230/EMTG_153.git`)
2. Go to `/emtg`
3. Copy `EMTG-Config-template.cmake` and rename to `EMTG-Config.cmake` (`cp EMTG-Config-template.cmake EMTG-Config.cmake`)
4. In `EMTG-Config.cmake`
    1. Set 'CSPCICE_DIR' to wherever NASA’s SPICE toolkit has been downloaded (for example, `set(CSPICE_DIR /Users/nick/Documents/SPICE/cspice)`)
    2. Set 'SNOPT_ROOT_DIR' to point to SNOPT root location (for example, `set(SNOPT_ROOT_DIR /Users/nick/Documents/SNOPT/snopt7)`)
    3. Comment out all Boost related settings (using `#`)
    4. Change GSL path to wherever GSL `/lib` is (for example, `set(GSL_PATH /opt/homebrew/Cellar/gsl/2.7.1/lib)`)
    5. Change python requirement version to 3.10 (or 3.9, should still work)
5. Download `randutils.hpp` (from https://gist.github.com/gnzlbg/efbacf7c668f783cd021) and put into `/emtg/src/Math`
6. Create `build` folder in EMTG root (`mkdir build`)
7. Go inside `/build` (`cd build`)
8. Run `cmake ..`
9. Run `make` (warnings *should* be able to be ignored)

## Python
Standard procedures may be used for python installation and required packages (outlined in `/emtg/PyEMTG/README.txt`). We note that `wxpython` may require downloading a framework version of python. If using homebrew and pyenv workflow, then run `env PYTHON_CONFIGURE_OPTS="--enable-framework" pyenv install 3.10.4`

## To Run EMTG Through Python GUI
Again, follow instructions in `/emtg/PyEMTG/README.txt`:
1. Install proper python dependencies
2. Make `PyEMTG.options` file from template (`cp PyEMTG-template.options PyEMTG.options`) and update accordingly
Then run `python PyEMTG.py`

## General notes that might help
_ Make sure NASA's CSIPCE (https://naif.jpl.nasa.gov/naif/toolkit.html) is built for correct operating system


## Steps Takent to Modify Original EMTG Repository
1. Clone original EMTG repository (https://github.com/nasa/EMTG)
2. Copy `EMTG-Config-template.cmake` and rename as `EMTG-Config.cmake`
3. In `EMTG-Config.cmake`
    1. Set 'CSPCICE_DIR' to wherever NASA’s SPICE toolkit has been downloaded (download to anywhere other than EMTG root)
    2. Set 'SNOPT' to point to SNOPT root location (anywhere other than EMTG root)
    3. Comment out all Boost related settings
    4. Change GSL path to wherever GSL `lib` is
    5. Change python requirement version to 3.10 (or 3.9, should still work)
4. Download `randutils.hpp` (from https://gist.github.com/gnzlbg/efbacf7c668f783cd021) and put into `emtg/src/Math/`
5. In `emtg/CMakeLists.txt`, change file extension in lines 206 and 207 from `.so` to `.dylib`
6. In `emtg/Utilities/file_utilities.cpp`
    1. Comment out line 22
    2. Add `#include <filesystem>`
    3. Change `namespace fs = ::boost::filesystem` to `namespace fs = std::filesystem`
7. In `emtg/Utilities/file_utilities.h`
    1. Comment out lines 23 and 24
    2. Comment out line 29
    3. Add `#include <filesystem>`
    4. Change `namespace fs = ::boost::filesystem` to `namespace fs = std::filesystem`
8. In `emtg/src/Executable/EMTG_v9.cpp`
    1. Comment out line 50
    2. Add `#include <filesystem>`
    3. Change all `boost::filesystem` to `std::filesystem` (use search and replace), remove all `::` from `::std::filesystem` where necessary 

# Original nasa/EMTG README

Welcome to EMTGv9 Open Source Release
README.opensource file compiled by Jacob Englander 4-20-2020

This package contains all of the code that NASA GSFC is releasing for the Evolutionary Mission Trajectory Generator, version 9. No third-party code is included in this release. You will need to download the third-party components yourself. This information is detailed in documents that may be found in the docs/build_system folder.

In particular, you will need a license for SNOPT. This is not free. Many of you probably already have SNOPT, and the rest may have to purchase it. EMTGv9 is known to work with SNOPT 7.5 and 7.6. It probably also works with 7.7 but I can't verify this because I don't have a license for 7.7. Older versions *might* work.

You will want to download SPICE ephemeris files for the bodies that you are visiting. I recommend downloading the full 3+ GB set from http://naif.jpl.nasa.gov/naif/ and placing it in your Universe/ephemeris_files folder

Note that regardless of whether you download all of the various SPICE ephemerides for the solar system, you will need to download de430.bsp from https://naif.jpl.nasa.gov/pub/naif/generic_kernels/spk/planets/de430.bsp and place it in your Universe/ephemeris_files folder. I could not supply this file because it is too large for Github.

You must edit the EMTG-Config.cmake file as follows:

1) Change the CSPICE_DIR to wherever you have placed your CSPICE package. CSPICE_DIR should point to the root CSPICE directory.

2) Change the SNOPT_ROOT_DIR to be wherever you have placed the unzipped SNOPT folder.  From this directory, it will seek the traditional /lib and /interface folders (as appropriate to your SNOPT version, which should be auto detected).  Report any problems to the EMTG lead developers.
	Example: This is currently usually a directory called "snopt7"

3) Change the 'BOOST_ROOT' to the root of your boost installer.  This presupposes you have already built boost (run its bootstrapper)
		3a) Optionally, if during the run phase of CMAKE boost is not being found, uncomment the next two lines and specifically specify the BOOST_INCLUDEDIR and BOOST_LIBRARYDIR.  The default options are traditional variants that are sometimes chosen by boost installers, but yours may vary.

Once all the above is in place, open CMake, and point the source directory at the EMTG root directory.  Point the build directory to the directory of your choice (may be the same). Run configure, then Generate, as per the usual CMake Process. This will create either  makefile for Linux or a Visual Studio project for Windows, or an Xcode project for Mac.

EMTGv9 is provided "as is" in its imperfect but very capable state. The US Government, NASA, and the developers cannot guarantee that the results you produce with EMTG are correct. EMTG is intended for use as a trade study tool and an initial guess generator for a flight navigation tool. We recommend that you use it in those contexts and only those contexts.
