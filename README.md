# weacast-grib2json

[![Build Status](https://travis-ci.org/weacast/grib2json.png?branch=master)](https://travis-ci.org/weacast/grib2json)
[![Code Climate](https://codeclimate.com/github/weacast/grib2json/badges/gpa.svg)](https://codeclimate.com/github/weacast/grib2json)
[![Test Coverage](https://codeclimate.com/github/weacast/grib2json/badges/coverage.svg)](https://codeclimate.com/github/weacast/grib2json/coverage)
[![Dependency Status](https://img.shields.io/david/weacast/grib2json.svg?style=flat-square)](https://david-dm.org/weacast/grib2json)
[![Download Status](https://img.shields.io/npm/dm/grib2json.svg?style=flat-square)](https://www.npmjs.com/package/grib2json)

A command line utility that decodes [GRIB2](http://en.wikipedia.org/wiki/GRIB) files as JSON.

This utility uses the netCDF-Java GRIB decoder, part of the [THREDDS](https://github.com/Unidata/thredds) project
by University Corporation for Atmospheric Research/Unidata.

Installation
------------

This requires Java to be installed on your system and the JAVA_HOME environment variable to be positioned.

```
git clone <this project>
```

The project contains a bin/lib folders with the latest version.

Build
-----

This requires Maven to be installed on your system.

```
git clone <this project>
mvn package
```

This creates a .tar.gz in the target directory. Unzip and untar the package in a location of choice.
The package contains a bin/lib folders that should be at the same hierarchical level (e.g. in /usr).

A Docker file is also provided to get the tool ready to work within a container, from the project root run:
```
docker build -t weacast/grib2json .
```

Usage
-----

The `grib2json` launch script is located in the `bin` directory and requires the `JAVA_HOME` environment
variable to be defined.

```
> grib2json --help
Usage: grib2json [options] FILE
	[--compact -c] : enable compact Json formatting
	[--data -d] : print GRIB record data
	[--filter.category --fc value] : select records with this numeric category
	[--filter.parameter --fp value] : select records with this numeric parameter
	[--filter.surface --fs value] : select records with this numeric surface type
	[--filter.value --fv value] : select records with this numeric surface value
	[--help -h] : display this help
	[--names -n] : print names of numeric codes
	[--output -o value] : write output to the specified file (default is stdout)
	[--verbose -v] : enable logging to stdout
```

For example, the following command outputs to stdout the records for parameter 2 (U-component_of_wind), with
surface type 103 (Specified height level above ground), and surface value 10.0 meters from the GRIB2 file
_gfs.t18z.pgrbf00.2p5deg.grib2_. Notice the optional inclusion of human-readable _xyzName_ keys and the data array:

```
> grib2json --names --data --fp 2 --fs 103 --fv 10.0 gfs.t18z.pgrbf00.2p5deg.grib2

[
    {
        "header":{
            "discipline":0,
            "disciplineName":"Meteorological products",
            "gribEdition":2,
            "gribLength":27759,
            "center":7,
            "centerName":"US National Weather Service - NCEP(WMC)",
            "parameterNumber":2,
            "parameterNumberName":"U-component_of_wind",
            "parameterUnit":"m.s-1",
            "surface1Type":103,
            "surface1TypeName":"Specified height level above ground",
            "surface1Value":10.0,
            ...
        },
        "data":[
            -2.12,
            -2.27,
            -2.41,
            ...
        ]
    }
]
```

When using the tool as a Docker container the arguments to the CLI have to be provided through the ARGS environment variable, with the data volume required to make input accessible within the container and get output file back the previous example becomes:
```
docker run --name grib2json --rm -v /mnt/data:/usr/local/data -e "ARGS=--names --data --fp 2 --fs 103 --fv 10.0 --output /usr/local/data/output.json /usr/local/data/gfs.t18z.pgrbf00.2p5deg.grib2" weacast/grib2json
```
