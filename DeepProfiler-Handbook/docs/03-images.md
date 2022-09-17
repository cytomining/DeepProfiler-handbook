# 3. Images and segmentation masks

## **3.1 Images**

DeepProfiler expects either TIFF or PNG files. Image channels need to be stored in separate files. Image sizes and dimensions are flexible. DeepProfiler are compatible with most high-throughput microscopy systems.

## **3.2 Masking cells**

The pixels in an image that belong to cells can be identified using segmentation algorithms, such as those available in [CellProfiler](https://cellprofiler.org/). The segmentation boundaries can be stored as binary images with the outlines of cells in white on a black background. These outlines can be used in DeepProfiler to mask cells and isolate the content of single cells for training neural networks and computing features. This may be useful for projects where the structure of single cells determines the phenotype of interest. In our experience, masking cells can sometimes reduce the performance of learning algorithms in identifying phenotypic information. If cell context is necessary for the study of phenotypic variations in your dataset, we recommend you skip cell masking.

To mask cells, you first need to segment them using an external tool like [CellProfiler](https://cellprofiler.org/) or [Ilastik](https://www.ilastik.org/) and save the binary image of their outlines. Save these images in the `input/outlines` directory. Next, you need to create a csv file with the list of the outline images with three additional columns: plate, well and site. Save this file in the `input/metadata` directory. The final step is to update the configuration file ([Section 3](#heading=h.5i3187icaj4t)) to let DeepProfiler know that you want to use these outlines for masking the cells. Specifically, the `mask_objects `setting under `locations` must be set to `true` in the configuration file to use masks (more details below).
