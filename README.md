# EMTG_153 Instructions
The following instructions are for setting up NASA's EMTG on Mac. This repository is a forked version of the original EMTG repository and has been edited to ensure it works on Mac, assuming the rest is setup correctly as shown below. Changes that were made between this repository and the original repository may be viewed in the code itself, but details of the changes are also outlined at the end of this README.

## Prerequisites
Below are things that need to be done before EMTG can be built.

### SNOPT

## Building EMTG_153

## Steps Takent to Modify Original EMTG Repository

1. Clone original EMTG repository (https://github.com/nasa/EMTG)
2. Copy 'EMTG-Config-template.cmake' and rename as 'EMTG-Config.cmake'
3. In 'EMTG-Config.cmake'
    1. Set 'CSPCICE_DIR' to wherever NASA’s SPICE toolkit has been downloaded (download to anywhere other than EMTG root)
    2. Set 'SNOPT' to point to SNOPT root location
    3. Comment out all Boost related settings
    4. Change GSL path to wherever GSL root is
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
