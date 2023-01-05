# MergeGI

[![Tests](https://github.com/sequana/MergeGI/actions/workflows/main.yml/badge.svg)](https://github.com/sequana/MergeGI/actions/workflows/main.yml)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/mergegi)
[![Coverage Status](https://coveralls.io/repos/github/sequana/MergeGI/badge.svg?branch=main)](https://coveralls.io/github/sequana/MergeGI?branch=main)
[![PyPI version](https://badge.fury.io/py/mergegi.svg)](https://badge.fury.io/py/mergegi)


**MergeGI** provides a single command line to merge and select barcoded raw data from [MGI](https://en.mgi-tech.com/products/) sequencing runs into a set of FastQ files ready for subsequent bioinformatics analysis.


- [Installation](#installation)
- [Getting Started](#getting-started)
- [General Usage](#general-usage)


## Installation

We provide **MergeGI** as a Python library available on [Pypi](https://pypi.python.org). The standalone application is called **mergegi** and can be installed in an environment with Python>3.7 as follows:

    pip install mergegi

There is no dependencies except for **click** package so that installation should be straightforward. 


For developers:

    git clone git@github.com:sequana/MergeGI.git
    cd MergeGI
    pip install -e .[testing]

## Apptainers

MergeGI is available as an apptainer image within the https://damona.readthedocs.io project. For example, the version 0.1.0 is available here:  https://sandbox.zenodo.org/record/1134857/files/mergegi_0.0.1.img  (60Mb).


## Overview

The main goal of **MergeGI** is to select and merge the FastQ files generated by a MGI sequencer into a list of FastQ files directly usable for subsequent bioinformatics analysis. Why do we need to do this preprocessing ? 

First, MGI generates one FastQ file per barcode. You may not need all those barcodes yet the demultiplexing performs a systematic search of all barcodes. Consequently, you will end up with FastQ files corresponding to your barcode and a bunch of FastQ files  that should be ignored. Given the information from your wetlab colleagues you should have the list of samples and their relevant barcodes. 

Second, MGI technologies imposes that barcodes being processed in a specific manner meaning that a given sample may be split into several barcodse (files). Therefore we need a tool to merge such files. Again, the wetlab should provide the barcodes corresponding to a given sample. See image below for more explanation

Third, a MGI flowcell has several lanes. You may want to merge the lanes or not. 

Those 3 steps should be managed seemlessly by our tool given a sample sheet and the output directory of the MGI runs.

## General Usage and Examples


The data structure expected by **MergeGI** is the expected output directoy of MGI runs:

    OutputFq/Flowcell/L01
    OutputFq/Flowcell/L02

Where L01/L02 stands for lane 1 and 2.

The software needs a sample sheet that describe the sample name, the associated barcode identifier, the project name (it will be used to create the new output directory), and the lane where is the sample/barcode pair. Here is an example:

```csv
samplename,barcode,project,lane
A,         1,      projectA, 1
B,         20,     projectA, 1
A,         1,      projectA, 2
C,         20,     projectB, 2
C,         30,     projectB, 1
B,         30,     projectA, 2
```

If you have pooled a sample on the four lanes, meaning it is the same barcode on each lane, you can use the * character to simplify the sample sheet:

```csv
samplename,barcode,project,lane
A,         1,     ,projectA, *
B,         20,    ,projectA, *
```

> **_IMPORTANT NOTE:_**  The header must be present. The header names are not important but columns must be sorted with the expected order: sample name, barcode, project name, lane. 


Given the sample sheet, and the input directory (top level of the MGI runs), this command should create a new clean directory with the relevant FastQ files (here in merge_data directory):

```bash
mergegi --samplesheet samplesheet.csv --input-directory mgi_raw_data --output-directory merge_data 
```

If the data is paired, add *--paired* argument

```bash
mergegi --samplesheet samplesheet.csv --input-directory mgi_raw_data --output-directory merge_data --paired
```


By default, lanes are merged. If this is not what you want you may disable this option:

```bash
mergegi --samplesheet samplesheet.csv --input-directory mgi_raw_data --output-directory merge_data --paired --no-merge
```

## Changelog

| Version  | Description  |  
|----------|---
| 0.1.0    | simplify CI and use pyproject |
| 0.0.0    | firs release  |


## Barcode distribution example

<img src="doc/bccode.png" width="50%">





