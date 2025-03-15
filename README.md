# MRI_Preprocessing

In this project we're going to build a pipeline to preprocess the Prostate MRI pictures and for next project, I'm going to build a CNN Model for detect the tumor.  

This project selected based on [PI-CAI Challenge](https://pi-cai.grand-challenge.org/) for more details check their site.

The dataset is form [zenodo](https://zenodo.org/records/6624726) and only [picai_public_images_fold0.zip](https://zenodo.org/records/6624726/files/picai_public_images_fold0.zip?download=1) folder which contain only 295 pictures of 1500 picture of this dataset.  

To Realize what are the suffix of image files read **doc.md**.

## Outline

This project contain several parts and it's going to complete as further I go. The different parts of the project are as follows:

+ Part1
    1. Import Libraries
    2. Find Path
    3. Show Metadata
    4. Separate DataFrame by filetype
    5. EDA of DataFrames
    6. EDA of Pictures
    7. Data Cleaning (MetaData & Pictures)
    8. Making Dataset
+ Part2
    1. Import Libraries
    2. Load Datasets
    3. Histogram of Height, Width and channels of pics
    4. Add Label to Dataframes
    5. Image Normalization 
    6. Noise Handling and Contrast Enhancement 

In the following sections, we will explain each part individually.

## 1. Import Libraries

In This part of code I import libraries that need further in the project.

_**SimpleITK**_: This library is for manipulate, see and work with files with **.mha** suffix.  
 _**os & Shutil**_: For working with system by using python code.  
_**Numpy**_: For manipulating the values of pictures.
_**Pandas**_: For making csv files for images.  
_**matplotlib & seaborn**_: For visualization.  

## 2. Find Path

First problem I have is Access images. So I write an script to access images. In this part I try to find the path of all suffix individually and save them in a dictionary.

## 3. Show Metadata

In this part I write an function for showing the metadata of images.(`show_metadata`). The output of data is like this:

>0008|0020: 2019-07-02  
0008|0060: MR  
0008|0070: SIEMENS  
0008|1090: Skyra  
0010|0020: 10000  
0010|0040: M  
0010|1010: 073Y  
0012|0062: YES  
0020|000d: 1000000  
ANONYMISATION_SCRIPT: PI-CAI anonymisation script v2.0  
ITK_InputFilterName: MetaImageIO  
ITK_original_direction: [UNKNOWN_PRINT_CHARACTERISTICS]  
ITK_original_spacing: [UNKNOWN_PRINT_CHARACTERISTICS]  
Modality: MET_MOD_UNKNOWN  
PROSTATE_VOLUME_REPORT: 55  
PSAD_REPORT: nan  
PSA_REPORT: 7.7  

For understand that what are these I use [innolitics](https://dicom.innolitics.com/) site and search in internet. For Example I search, "_0008, 0020 innolitics_" in google and figure out the this is the date.

## 4. Separate DataFrame by filetype

I decided, based on suffixes, made DataFrames and store the metadata in those DataFrames, So I built `df_builder` function that takes takes an image file and columns of that image and returns the DataFrame. At the end of this part I decided that add path of files in the DataFrame too. 

## 5. EDA of DataFrames
In this part, I checked duplication of instances, datatype of columns and handling of missing values.  
### Duplication of Instances
There was no duplication.
### DataType of Columns
I Figured out that the datatype of all columns is object. It means that they are all string. So I have to change them. I changed date to datetime, age to int, Diffusion_sensitization_factor sec/mm, Modality_(Repeated), PROSTATE_VOLUME_REPORT, PSAD_REPORT and PSA_REPORT to float.
### Handling Missing Values
These was some `"nan"` and `"MET_MOD_UNKNOWN"` in dataset, which I changed them to `np.nan`.

The Output of EDA is something like below:
```
Data columns (total 19 columns):
 #   Column                                 Non-Null Count  Dtype         
---  ------                                 --------------  -----         
 0   Study_Date                             300 non-null    datetime64[ns]
 1   Modality                               300 non-null    object        
 2   Manufacturer                           300 non-null    object        
 3   Manufacturer's_Model_Name              300 non-null    object        
 4   Patient_ID                             300 non-null    object        
 5   Patient's_Sex                          300 non-null    object        
 6   Patient's_Age                          300 non-null    int64         
 7   Patient_Identity_Removed               300 non-null    object        
 8   Diffusion_sensitization_factor sec/mm  300 non-null    float64       
 9   Study Instance_UID                     300 non-null    object        
 10  ANONYMISATION_SCRIPT                   300 non-null    object        
 11  ITK_InputFilterName                    300 non-null    object        
 12  ITK_original_direction                 300 non-null    object        
 13  ITK_original_spacing                   300 non-null    object        
 14  Modality_(Repeated)                    0 non-null      float64       
 15  PROSTATE_VOLUME_REPORT                 296 non-null    float64       
 16  PSAD_REPORT                            207 non-null    float64       
 17  PSA_REPORT                             293 non-null    float64       
 18  path                                   300 non-null    object        
dtypes: datetime64[ns](1), float64(5), int64(1), object(12)
```


## 6. EDA of Pictures
In this part I decide to check the attributes like Dimension, Spacing, Depth, Direction and Shape of image.

>Dimension shows, how many dimension does the image has.  
Spacing shows the distance between pixels or voxels in each dimension.  
Direction of an image refers to the orientation of the coordinate system.

Also I recognize that the shape of images are not the same.  
In the `show_image`function, I show the images.

## 7. Data Cleaning (MetaData & Pictures)
In this part, as has been said in [Clinical and Scanner Information](https://pi-cai.grand-challenge.org/DATA/) so many features need to be drop. so In this section I do this. Also I a set `Study Instance UID` or `Study Instance_UID` for index of datasets.  
Since the rest of the features are very important, I can't fill `nan` values with inaccurate values, so I delete them.

## 8. Making Datasets
The reason that I separate this code in to several parts, is that I can modify and manipulate better and also I have checkpoints in my code execution. This part made checkpoint and save the dataset of pictures.

## 9. Histogram of Height, Width and channels of pics 
In this part I plot histogram of Height, Width and slices of pictures. I do this for find out the distribution of my data (pictures).

## 10. Add Label to Dataframe
Based on Dataset I find out that the label of dataset, stored in [marksheet.csv](https://github.com/DIAGNijmegen/picai_labels/blob/main/clinical_information/marksheet.csv). I stored this file in this repository and you can access to it. labels are stored in `case_csPCa` column.

## 11. Image Normalization
In this part I Normalize the intensity of voxels and based on distributions which are shown in this part I select standard Normalization, as you can see, in charts I have to align the intensity in to one value, in doesn't matter that counts of them are different  but they has to be like each other.

## 12. Noise Handling and Contrast Enhancement
In this part I use bilateral filter, gaussian filter and median filter for Noise handling and non of them works well. for contrast enhancement I use clahe and as we can see in pictures and based on PSNR and SSIM that the contrast of pictures are getting better.

