# Overview

This directory contains experimental python 3.8+ script to test NetCDF4 encoding options for a GGXF.
These scripts are intended for experimenting with the GGXF format - they are not "production ready".  

## GGXF module

The GGXF directory contains an under development.  It is intended as a prototype python module for handling GGXF files.  There is also a placeholder script GGXF.py in the source directory which just runs the App.py script in the GGGXF directory.

* Reading and writing a NetCDF4 GGXF file
* Reading and writing a YAML format GGXF file
* Calculating the GGXF grid value at a set of points in a CSV formatted file (with columns X and Y corresponding to the first and second ordinates of the interpolation coordinate system).  If there is more than one group in the GGXF file it calculates values for every group.  This currently does not handle deformation models or time functions and only implements bilinear interpolation.
* Dumping a single grid from the GGXF file as a CSV file of coordinates and values with one row for each grid node (mainly for checking the grid layout/affine transformation is correct)
* Adding deformation model time functions and calculating the deformation model
* Implementing biquadratic and bicubic interpolation functions
* Checking the GGXF content attribute to ensure the content type is correct and group parameters match the content type.

Intended development includes:

* Handling of summation of uncertainty for deformation.  RMS?
* Support for "no-data" value
* ? Handling of covariance for deformation
* ? Support for non-GGXF data in grid
* ? Lazy loading of grid data
* ? Consider the use of NetCDF packing for efficient storage of data (eg automatic testing of parameter range and implementing packing were suitable)
* Test suite

If it is to be adapted for production use it will also require

* A lot of documentation
* Packaging

The scripts/GGXF.py runs a basic command line application for testing.  The options provided by this will change as the program is developed.  To see the command line usage run the command

``` sh
python3 GGXF.py -h
```

Options listed at the time of writing are:

``` text

usage: Read, save, calculate a GGXF file  [-h] 
                                          [-o filename] 
                                          [-n option=value] 
                                          [-y option=value] 
                                          [-c csv_filename] 
                                          [-e epoch] [--base-epoch epoch]
                                          [--output-csv-file output_ssv_filename] 
                                          [--csv-decimal-places csv_summary_file] 
                                          [--csv-summary csv_summary_file] 
                                          [--list-grids]
                                          [--dump-grid grid_id csv_file] 
                                          [-g] 
                                          [-v]
                                          ggxf_file

positional arguments:
  ggxf_file             Name of GGXF file to load - .yaml for YAML format

optional arguments:
  -h, --help            show this help message and exit
  -o filename, --output-ggxf-file filename
                        Save GGXF to file - .yaml for YAML format
  -n option=value, --netcdf4-option option=value
                        Options for NetCDF4 files (see below for options)
  -y option=value, --yaml-option option=value
                        Pptions for YAML files (see below for options)
  -c csv_filename, --coord-csv-file csv_filename
                        CSV file of points to calculate - assumes column headers with X, Y columns
  -e epoch, --epoch epoch
                        Epoch at which to calculate GGXF
  --base-epoch epoch    Base epoch for calculating change between epochs
  --output-csv-file output_ssv_filename
                        CSV file to convert - assumes column headers with X, Y columns (default based on input)
  --csv-decimal-places csv_summary_file
                        Number of decimal places for CSV calculated values
  --csv-summary csv_summary_file
                        Write a grid summary to the named CSV file
  --list-grids          Print a list of grids with ids
  --dump-grid grid_id csv_file
                        Dump a specific grid to a CSV file (use --list-grids to get grid ids)
  -g, --debug           Generate debugging output
  -v, --verbose         More verbose output

This is a proof of concept implementation to evaluate encoding options.
The output is not necessarily an "authoritative" GGXF file

The following options apply to NetCDF input (I) and output (O):

  "use_nested_grids" (O) Generate NetCDF with nested grid definition (true or false, default true)
  "write_cdl" (O) Generate an output CDL file as well as a NetCDF file (default false)
  "use_compound_types" (O) Use compound types (very limited test implementation) (default false)

The following options can apply to YAML format input (I) and output (O):

  "grid_directory" (I) Base directory used for external grid source names
  "check_datasource_affine_coeffs" (I) Compare affine coeffs from data source with those defined in YAML (true or false)
  "use_nested_grids" (O) Create nested grids in the output YAML (true or false, default true)
  "use_griddata_section" (O) Use a gridData section for grid data (true or false, default true if more than one grid)
  "write_headers_only (O) Write headers only - omit the grid data"

```

## gdal_to_ggxf_grid_headers.py

A basic script to read a GDAL grid data source and emit a YAML fragment containing the corresponding grid definitions that can be used install the grids into a GGXF YAML file.  If this is useful it could be enhanced to provide a more complete YAML template.  Note: GGXF.py has been amended to support inferring these parameters from a GDAL data source and not requiring them in the YAML header, and also checking the data source matches the YAML.  
