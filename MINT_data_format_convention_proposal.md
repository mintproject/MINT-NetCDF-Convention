# MINT Data Format Convention Proposal
- Target: Structured gridded data
- Version: Draft 3
- Author/POC: Hayley Song (haejinso@usc.edu)
- Last Modified: Sep 24, 2019


The purpose of this document is to propose a self-describing data format for structured gridded datasets for MINT data catalog and visualization based on the netCDF and the CF convention. 


## Brief summary on NetCDF 
NetCDF (network Common Data Form) is a file format for storing multidimensional scientific data (`variables`) such as pressure, surface temperature, soil moisture content and wind speed. It has been adopted as a standard way to represent scientific data as it facilitates data access. Its main advantages are<sup>[1](#myfootnote1)</sup>:
  - self-describing: it includes information about the data it continas so that no external tables are needed to interpret it
  - machine-independent: portable  across different platforms, eg. MacOS, Windows, Linux
  - scalable: a small subset of a large netCDF file can be accessed efficinetly without reading the entire file 
  
[^1]: A quick tour of netCDF data: [link](https://is.gd/Kwh6R2)

We would like to start a discussion on the common convention for structured gridded data for MINT by proposing the following specifications.  We have consulted the Unidata group's [recommendation](#) on the netCDF attributions for data discovery as well as the [CF Conventions](http://cfconventions.org/), the [ACDD Conventions](https://is.gd/IKZ3CQ), and the Open Geospatial Convention (document)[https://tinyurl.com/yyoz4bxb]. 

The purpose of this specification is to support efficient visualization and data exchange within the MINT project. We welcome your comments, questions, and any suggestions you might have. Please submit your comments [here](https://docs.google.com/spreadsheets/d/1eT_Z51R4VwVen-qx7XGtNjoHuc2sMlYmmxbi5acumx0/edit?usp=sharing). 


## Contents
NetCDF convention for MINT structured gridded datasets
0. Required dimensions

1. Attribute Convention 
    - Global attributes: per data file 
    - Variable attributes: per variable
<!--
2. Metadata Convention 
-->
2. Examples

3. Related materials 

<!--
8 [todo] conformance test webapp
-->

---
## Dimensions Convention
MINT NetCDF Visualization requires the input NetCDF file to have the following three dimensions specified.
Any violation to the naming convention will cause an error in visualization.

| Name | Description | Required fields |
| --- | --- | --- |
| X | Longitude | unit (eg: km)* |
| Y | Latitude | unit (eg: km)* |
| time | Time | unit (as a valid udunit)* |
| bnds | Number of bands, ie. dimensionality of a value in each grid cell. (eg: 1 for a scalar, 3 for RGB, 4 for RGBA) | 


 units*:  units of the data contained by the variable; must be a valid [udunits](https://is.gd/UVgrOm) string. 
 For example, “m” (meter), “km” (kilometer), “degrees_north” (for latitude), “degrees_east” (for longitude),

### Units
1. Time

Two conventions for storing a date/time into a netCDF variable are:
- CF-compliant: as a numeric value with a udunits time unit, such as
    "seconds since 1992-10-8 15:00:00"
- ISO-compliant: as a string using ISO 8601 encoding, such as "2010-10-25T12:00:00Z".

### Example of dimensions following MINT convention
In addition to the unit field, each dimension is strongely encouraged to contain coordinates. For example:

 ```
 Dimensions:                 (X: 294, Y: 348, bnds: 2, time: 1)
Coordinates:
  * time                    (time) datetime64[ns] 2017-01-01
  * X                       (X) float64 22.05 22.15 22.25 ... 51.15 51.25 51.35
  * Y                       (Y) float64 -11.75 -11.65 -11.55 ... 22.85 22.95
Dimensions without coordinates: bnds
Data variables:
    Evap_tavg               (time, Y, X) float32 ...
    LWdown_f_tavg           (time, Y, X) float32 ...
    Lwnet_tavg              (time, Y, X) float32 ...
    Psurf_f_tavg            (time, Y, X) float32 ...
    Qair_f_tavg             (time, Y, X) float32 ...
    Qg_tavg                 (time, Y, X) float32 ...
    Qh_tavg                 (time, Y, X) float32 ...
    Qle_tavg                (time, Y, X) float32 ...
    Qs_tavg                 (time, Y, X) float32 ...
    Qsb_tavg                (time, Y, X) float32 ...
    RadT_tavg               (time, Y, X) float32 ...
    Rainf_f_tavg            (time, Y, X) float32 ...
    SM01_Percentile         (time, Y, X) float32 ...
    SWdown_f_tavg           (time, Y, X) float32 ...
    SoilMoi00_10cm_tavg     (time, Y, X) float32 ...
    SoilMoi100_200cm_tavg   (time, Y, X) float32 ...
    SoilMoi10_40cm_tavg     (time, Y, X) float32 ...
    SoilMoi40_100cm_tavg    (time, Y, X) float32 ...
    SoilTemp00_10cm_tavg    (time, Y, X) float32 ...
    SoilTemp100_200cm_tavg  (time, Y, X) float32 ...
    SoilTemp10_40cm_tavg    (time, Y, X) float32 ...
    SoilTemp40_100cm_tavg   (time, Y, X) float32 ...
    Swnet_tavg              (time, Y, X) float32 ...
    Tair_f_tavg             (time, Y, X) float32 ...
    Wind_f_tavg             (time, Y, X) float32 ...
    time_bnds               (time, bnds) datetime64[ns] ...
 ```
 
## Attribute Convention
Note that the attribute names link to the Unidata definitions, and each element is marked with `M`,`R`,`O`, or `C` 
depedning on our specificationn requirement

  - `M`: Mandatory    
  - `R`: Recommended
  - `O`: Optional
  - `C`: Mandatory under certain conditions
      - eg. If the dataset has a time dimension, `time_coverage_start` field is mandatory. In this case we mark `time_coverage_start` as `C`
      - Similarly, if the dataset has a spatial dimension, `geospatial_bounds_crs` field is mandatory, and will be marked as `C`


1. Global attributes
	 - Where time is specified as an attirubte, the [IOS 8601]([https://en.wikipedia.org/wiki/ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)) standard should be used
	 
 <!--[todo-add datatype column]-->
 
| Attribute | Requirement | Description | Example | 
|:----------|:------------:|:------------|:--------|
 |title    | M | a short description of the dataset||
| summary | R | a paragraph describing the dataset||
| naming_authority | M | the organization that provides the dataset id (below). We recommend using URIs or reverse-DNS naming | edu.isi.workflow
| id | M | UUID as generated by MINT's data catalog system |  |
| keywords | R| a comma separated list of keywords and phrasts||
| comment | O| micellaneous information about the data||
| date_created| M| the date on which the data was created||
| date_modified | M| the date on which this data was last modified | |
| date_issued | R | the date on which this data was formally issued| |
| creator_name | R | the data creator's name | |
| creator_email |M| the email address of the data creator | |
| institution |R| institution in charge of the dataset | | 
| project | R | the scientific project that produced the data | |
| history | R | a static value, "created by MINT workflow" | |
| convention | R | MINT-{versionNumber} | |


If the dataset has the time coordinates variable, the following extra specifications are required:
The value of the following attributes of the time coordinate variable must satisfi
| Attribute | Requirement | Description | Example | 
|:----------|:------------:|:------------|:--------|
|time_coverage_start|C||
|time_coverage_end|C||
|time_coverage_duration|R||
|time_coverage_resolution|C||
|time_units|C| “units since YYYY-MM-DD hh:mm:ss” string valuem| 


If the dataset has a geospatial coordinate variable, the following extra specifications are required/recommended:
- A cooridinate variable's data type must be a numerical type (usually floating point)

| Attribute | Requirement | Description | Example | 
|:----------|:------------:|:------------|:--------|
| geospatial_bounds_crs | C | | |
|geospatial_lat_min   | O | a short description of the dataset||
|geospatial_lat_max | O | a paragraph describing the dataset||
|geospatial_lon_min | O | dataset id |
|geospatial_lon_min | O | dataset id |
| geospatial_bounds | R | lon_min, lat_min, lon_max, lat_max||



2. Variable attributes

| Attribute | Requirement | Description | Example | 
|:----------|:------------:|:------------|:--------|
| title* | M | a brief description of the dataset| |
| standard_name* | R | a name for the variable from a standard list of names listed in the [Scientific Variables Ontology](http://www.geoscienceontology.org/) |
| long_name* | R | a long descriptive name for the variable ||
| units* | M | units of the data contained by the variable; must be a valid [udunits](https://is.gd/UVgrOm) string| “m” (meter), “km” (kilometer), “degrees_north” (for latitude), “degrees_east” (for longitude), “K” (temperature in Kelvin), “Pa” (pressure in Pascal)
| valid_min| M | |
| valid_max| M | |
| valid_range| M | |
|missing_value | M |
|fill_value  | M | |


<!--

---
## Metadata Convention

| Attribute | Requirement | Description | Example | 
|:----------|:------------:|:------------|:--------|
||||

---
# Example netCDF file 
The following is a netCDF file that follows our proposed conventions.
[todo]
-->

<!--
  ## Visualization example 
  [todo]
-->

---
## Related materials
- Unidata's CF Convention: [Official doc](http://cfconventions.org/latest.html), [Overview slides](https://is.gd/a2qEWQ)
- CF Metadata conventions [Official doc](https://is.gd/fhqNwq)
- List of netCDF Conventions - [link](https://www.unidata.ucar.edu/software/netcdf/conventions.html)
- CF standard name stable (for `standard_name` attribute) - [link](https://is.gd/FGU0JC)
- Attribute Convention for Data Discovery convention: [link](https://is.gd/KodyzB)
- udunits (for `units` attribute) - [Official doc](https://is.gd/H4xMXw)
- CRS (for CRS's epsg code)
- EPSG.io
- Example netCDF file - [link]()

## Related tutorials
1. https://github.com/mintproject/MINT-GeoViz/blob/master/examples/notebooks/01_xarray_intro.ipynb
	- How to use `xarray` (Python library) 
		- Read netcdf files as a Dataset and manipulate data
		- Create a Dataset representing a NetCDF  
			- Define labelled dimensions (optionally with coordinate data)
		- Write to a file


---
<a name="myfootnote1">1</a>: A quick tour of netCDF data: [link](https://is.gd/Kwh6R2)


