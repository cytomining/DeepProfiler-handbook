# 4. Metadata and single cell locations

DeepProfiler requires metadata files that provide information about the experiment as well as the locations of single cells (centroids) in your images. These files are stored in `project/inputs/` and there are two main types: 

1. A metadata **index.csv** file (located in `project/inputs/metadata/index.csv`) that relates image files to the experiment 
   structure (e.g., plate and well positions) and includes additional information about replicates and treatments.
2. A series of single cell **locations files** for each site (located in `project/inputs/locations/`) that provide the XY coordinates 
    of nuclei centroids. **Note** that this file is not required if running DeepProfiler in `full_image` mode.

## **4.1 The index.csv file**

The index.csv file is critical for running DeepProfiler. It follows a comma-separated-values (CSV) format with a header, 
contains information about the experiment, and lists all images in your project. DeepProfiler uses this file to guide image
sampling for running learning algorithms and to find the images that we want to process. This file is expected to contain 
metadata to identify the context of images in the physical experiment that produced them, for instance, identifiers of plates, 
wells and fields of view (i.e., sites; see {numref}`plate-fig`). 

```{figure} images/image3.png
---
name: plate-fig
---
Schematic of plates, wells and sites, which are three metadata fields required by DeepProfiler in the index.csv file.
```

DeepProfiler assumes that each row in the file represents one (multi-channel) field of 
view. The following list indicates the minimal columns that the index.csv file is expected to have:


1. `Metadata_Plate`: Name or identifier of the plate (i.e., the highest level of experimental organization), e.g. `41744`. The field header cannot be renamed.
2. `Metadata_Well`: Position in the plate, e.g. `A21` (i.e., middle level of organization within plates). The field header cannot be renamed.
3. `Metadata_Site`: A microscope acquires images in different sites within each well (i.e., the lowest level organization 
    within wells). For instance, sites may cover a 4x4 grid or a 9x9 grid, depending on resolution and other factors. 
    The site identifier for each image goes here, e.g. `3`. The field header cannot be renamed.
4. `Channel_Name`: Relative path to the image file of each channel. An experiment may have multiple imaging channels 
    (i.e., colors) and DeepProfiler assumes that each channel is stored in a separate image file (all channels stored in 
    the same file are not currently supported). Therefore, to put together all the channels of a single image, the index.csv 
    file will need to have multiple _channel_name_ columns listed in the configuration file. These columns can be renamed as 
    necessary and should point to the corresponding image files using a path relative to the image directory. For example, 
    an assay with DNA, RNA, and Mito stains will have three channel columns named accordingly, with entries in each column 
    pointing to the corresponding image file. The field may have different names.
5. `Treatment`: We assume that cells in a well have been treated in a biologically meaningful way or represent different 
    experimental conditions. This column keeps track of that information, which may have other names (in the provided example 
    data, it is called `pert_name`). It is useful as an identifier of the type of biological experiment, treatment, perturbation 
    or condition of cells observed in the images. This column must be a biologically meaningful label that could be used by 
    DeepProfiler for training purposes. May have different names.
6. `Replicate`: Number or identifier indicating which repetition of the treatment an image corresponds to. In the provided 
    example data it is called `pert_name_replicate`.

These are the minimum columns required in the index file. You can append more columns with information specific to your 
experiment as needed, to keep track of other metadata in your project. We recommend that if you do add additional columns, their names should not include any spaces. Notice that the order of columns is not important, as long as these are available. The meaning of the columns can be interpreted differently according to your problem, for instance, instead of plates, you may be interested in subjects or patients. However, the three levels of organization 
(plate, well, site) are expected, even if you don’t explicitly use them (e.g. set wells to a constant string if it does not apply to your data). The name of certain columns can be changed as well and later associated with the expected information in the configuration file [(Section 5)](../docs/05-config.md).

Click here to see an example [index file](https://github.com/cytomining/DeepProfiler-handbook/blob/00f3b41e753a40a1e3854a75bd491b7c1864a4da/DeepProfiler-Handbook/assets/index.csv).


Example image of an index.csv file:

```{figure} images/image4.png
---
name: index-file
---
Screenshot of an example index.csv file
```

## **4.2 single-cell locations file**

DeepProfiler needs the x and y coordinates for the centers of nuclei in your images. There should be one locations file for each site (i.e., each image) in your experiment. These files need to be stored in a particular structure: `project/inputs/locations/{Metadata_Plate}/{Metadata_Well}-{Metadata-Site}-Nuclei.csv`. The `Metadata_Plate`, `Metadata_Well` and `Metadata_Site` must match what is in the index.csv file. Here is an example of how to structure the locations data: 

```
.
└── project/
    └── inputs/
        └── locations/
            └── Plate1/
                ├── A01-f01-Nuclei.csv
                └── A01-f02-Nuclei.csv
                ...
```

The contents of each locations csv file for a particular site should be just two columns headed with `Nuclei_Location_Center_X` and `Nuclei_Location_Center_Y`. The pixel coordinates of centers for each nucleus should be listed in these columns (see below):

```
Nuclei_Location_Center_X,Nuclei_Location_Center_Y
219,34
175,45
1065,51
```

These coordinates can be generated by a program like CellProfiler if you segment nuclei with a module like IdentifyPrimaryObjects.
