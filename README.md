# HD Ground Database
This repository provides the HD Ground Database. A large database of high-resolution ground images from a downward-facing camera. It can be used, for example, to train and evaluate methods for ground texture based localization. For many images, we provide their ground truth poses.

## License
© 2022. This work is licensed under a CC BY-SA: 4.0 license.
https://creativecommons.org/licenses/by-sa/4.0/

## Paper
https://www.inf.uni-hamburg.de/en/inst/ab/cv/media/schmid-etal-icra-2022.pdf

## Citation
Please cite us if you are using our database.

```
@ARTICLE{HD_Ground_Schmid,
author={J. F. Schmid and S. F. Simon and R. Radhakrishnan and S. Frintrop and R. Mester},
journal={IEEE International Conference on Robotics and Automation (ICRA)},
title={{HD Ground} - A Database for Ground Texture Based Localization},
year={2022},
month={Mai},
pages={7628-7634},
}
```

## Get access
In order to get access to the data, please send an E-Mail to HD.Ground.Database@gmail.com with the following content:

- Name
- Affiliation
- Why would you like to get access to the data?

## Content of the HD Ground Database
The database contains images of four main application areas and seven additional areas.

The main datasets are:
- footpath_asphalt: Asphalt texture images from a footpath.
- parking_place_winter_dry: Cobblestone texture images from a parking place.
- office_carpet: Carpet texture images from an office.
- kitchen_laminate: Tile texture images from an office kitchen. These are made of plastic and the printed patterns repeat. However, the patterns are found offset in each case, because they have not been cut according to the period.
For each of these main application areas an additional square meter of the same texture, not being included in the main coverage area, is recorded:
- footpath_test_sq
- parking_place_test_sq
- office_test_sq
- kitchen_test_sq

The seven additional datasets are:
- doormat
- bathroom_tiles
- checker_plate_steel
- garage_concrete
- ramp_rubber
- terrace_pavement
- workroom_linoleum

In our paper, we use the datasets footpath_test_sq, parking_place_test_sq, office_test_sq, kitchen_test_sq, and doormat for training and parameter optimization. The datasets bathroom_tiles, checker_plate_steel, garage_concrete, ramp_rubber, terrace_pavement, workroom_linoleum are used to evaluate the generalization capabilities of localization methods, as they are not included during training and parameter optimization. The main datasets can provide regularly recorded test sequences to examine localization performance dependent on the time interval between mapping and localization, and they provide test sequences that can be used to evaluate localization performance in a teach-and-repeat scenario. 

For more details about the database please refer to our ICRA paper mentioned above.

### Ground truth poses
Ground truth poses are given as Euclidean transform matrices in homogeneous coordinates that represent a mapping of the image into the map coordinate system.
The poses are represented as a string of 9 values, e.g. "a b c d e f 0 0 1", which translates into the transform:

$$ T = \begin{bmatrix}
a & b & c \\
d & e & f \\
0 & 0 & 1
\end{bmatrix}.$$

With 

$$ t = \begin{bmatrix}
c \\
f 
\end{bmatrix},$$

representing the map coordinates of the top left corner of the image.
Simply multiply this matrix to a vector of image coordinates 

$$ T * \begin{bmatrix}
I_x \\
I_y \\
1
\end{bmatrix},$$

to obtain the corresponding map coordinates.
You may use the transform also to warp the entire image into the map using 'warpAffine()' from OpenCV for example.

### Structure of the Database
In the respective database folders, e.g. footpath_test_sq, you find folders containing the images, *.test files that list the local image paths for the sequence, and *.txt files that, in addition to the image paths, provide their 'ground truth' pose in the map coordinate system as standard euclidean transform in homogeneous coordinates (with the rows being concatenated after each other). Pose strings starting with "* " are incorrect or at least we were not able to confirm that they are correct (DO NOT USE THESE POSES AS GROUND TRUTH!). 
Usually subsequent images overlap with each other.

The following sequences are available:
- database: sequence of all reference images for the application area
- test_path: sequences used for training and parameter optimization, or for evaluation of generalization capabilities.
- regular_test_date: this is the regular test sequence of this texture recorded on date. All regular test sequences of a texture follow roughly (but not precisely) the same path.
- teach_and_repeat_date_num_a/b_f/r: sequence for the teach and repeat scenario recorded on date. Here, a rope is layed on the ground and followed precisely during the recordings.
-- num specifies the number of the rope setting that this sequence belongs to (there are 5 rope settings recorded per texture)
-- a/b specifies whether this is the first or second sequence of this rope setting
-- f/r specifies whether this is the first part of the sequence, in which the rope is followed in forward direction starting at the beginning of the rope and moving to its end with the rope being on the one side of the robot (e.g. on its left), or the second part, in which the rope is followed in reverse direction starting at its end and moving to its beginning with the rope being on the other side of the robot (e.g. on its right). See teach_and_repeat_setup.png for an illustration. In both cases, the robot is looking in driving direction.

### Image Names
Our naming scheme for the images is as follows:
HDG$VersionNumber_t$TextureNumber_$SequenceType_$DateOfRecording_s$SequenceNumber_c01_i$ImageNumber.png

The texture number mapping is:
- 001 : Footpath asphalt
- 002 : Parking place cobblestone
- 003 : Office carpet
- 004 : Kitchen laminate
- 005 : Doormat carpet
- 006 : Terrace pavement 
- 007 : Garage concrete
- 008 : Workroom linoleum
- 009 : Bathroom tiles
- 010 : Checker plate steel
- 011 : Ramp rubber

There are four sequence types:
- ref: reference image, i.e. an image from the initial scanning of the application area used as reference during localization.
- reg: regular test image, i.e. a (query) image from a regular test sequence.
- t-r: teach-and-repeat test image, i.e. a (query) image from a teach-and-repeat test sequence.
- train: query image for training and paramter optimization, i.e. an image of a test sequence from one of our training areas.
- general: test image used by us to evaluate generalization capabilities 


'c01' stands for the sensor (in this database its always camera number 1: c01) that recorded the image.

E.g. HDG1_t001_ref_2020-09-23_s0001_c01_i0000072.png
is the 72th image (recorded with our downward facing camera c01) of sequence 1 recorded on the 23th of September 2020;
it is a reference image of our asphalt application area and it is part of the first version of our HD Ground Database.

### Camera Calibration
We calibrated with a pinhole camera model with focal lengths fx, fy, principal point ppx, ppy, and two radial distortion parameters k1, k2. This resulted in the following values:
- [fx, fy, ppx, ppy, k1, k2] = [2195.70853, 2195.56073, 820.7289683, 606.242723, -0.1581150018, 0.1909030804]

Based on this camera model, the provided images have been transformed to compensate for lens distortion, using [cv::undistort()](https://docs.opencv.org/4.5.5/d9/d0c/group__calib3d.html#ga69f2545a8b62a6b0fc2ee060dc30559d). Furthermore, we compensated for vignetting as described in the paper.
