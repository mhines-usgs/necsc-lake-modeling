#NE-CSC lake modeling effort for MI, MN, WI state lakes

This project contains all raw to intermediate data processing that goes into the modeling project [mda.lakes](https://github.com/USGS-R/mda.lakes) and the lake attributes package [lakeattributes](https://github.com/USGS-R/lakeattributes).

The building of the files is tracked from their raw (canonical) data sources and their intermediate storage after processing. Processing includes subsetting, filtering, aggregating, and averaging. 

## data types for this project

| file     | description                                                        |
|--------------|:-------------------------------------------------------------|
| `NHD_sub`     | [National Hydrography Dataset](http://nhd.usgs.gov/) is pulled from ftp, and subsetted to include area of interest   |
| `NHD_summ`    | `NHD_sub` is summarized into a .tsv hosted in this repository |
| `NLDAS_sub`   | the [North American Land Data Assimilation System](http://ldas.gsfc.nasa.gov/nldas/) data is subset to the area of interest (the extent of the NHD file) and uploaded to a thredds server for processing by the [Geo Data Portal](http://cida.usgs.gov/gdp) |
| `NLDAS_data`    | `NLDAS_sub` is processed to create lake-specific driver files according to the permIDs in `NHD_summ` |
| `NLDAS_summ`    | `NLDAS_data` is summarized into a .tsv hosted in this repository |
| `clarity_data`  | Water clarity data from the [Water Quality Portal](http://waterqualitydata.us/) and mapped to permIDs in `NHD_summ` |
| `clarity_summ`  | `clarity_data` is summarized into a .tsv hosted in this repository |
| `depth_data`  | Lake depth data mapped to permIDs in `NHD_summ` |
| `temperature_data`  | Water temperature data from the [Water Quality Portal](http://waterqualitydata.us/) and mapped to permIDs in `NHD_summ` |
| `temperature_summ`  | `clarity_data` is summarized into a .tsv hosted in this repository |

## development guidelines  

### configurations
files within the `configs` folder are used to define the processing routines, and are also dependencies of the make-like system used for data processing ([remake](https://github.com/richfitz/remake)). We are currently setting these config files up for each step above in the data types table, and they should be named appropriately relative to that convention (e.g., "configs/NHD_sub_config.yml").  

### functions vs scripts
since we are using [remake](https://github.com/richfitz/remake), processing scripts should be based on functions that have a working directory at the top level of this project. These functions can take inputs that are related to other outputs, or can internally define processing parameters based on the `config` for the file output.

### file outputs
Each function should have some kind of trackable output that stays locally. For example, `NHD_sub` is an external file that is posted as a result of processing, but `NHD_summ` is a summary file that holds data that summarizes that file and any of the important processing steps (and warnings if applicable).  

Most files generated by this project will not be hosted by this repository as they are too large. Details for accessing them from their actual destinations should be included in the `_summ` files. 


## dependencies
`yaml`, `remake`, `storr`, `dplyr`, `rgeos`, `rgdal`, `geoknife` are used. These dependencies should also be tracked in the `remake` yaml files. 

### Building the data files

Starting to use `remake` package to deal with the dependencies of inputs and outputs for processing data

```r
devtools::install_github("richfitz/storr")
devtools::install_github("richfitz/remake")

# then from the top level directory, 

library('remake')
make()
```
