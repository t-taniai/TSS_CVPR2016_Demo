This application provides executable binaries for a demonstration of the following paper.
If you use our algorithm, please cite our CVPR 2016 paper.
We do not plan to release our original source code of the algorithm due to the lisenced issue.
Our dataset and results are available at http://taniai.space/projects/cvpr16_dccs/
We also provide an evaluation and visualization kit at https://github.com/t-taniai/TSS_CVPR2016_EvaluationKit

@InProceedings{Taniai2016,
	author = {Tatsunori Taniai and Sudipta N. Sinha and Yoichi Sato},
	title = {{Joint Recovery of Dense Correspondence and Cosegmentation in Two Images}},
	booktitle = {IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
	month = {June},
	year = {2016}
}

--------
Download:
--------


-----
Usage:
-----
Run the demo.bat file.
It first runs feature extraction and then runs the main algorithm.
There are following input and output data.

/data (input)
	- image1.png : One of input images.
	- image2.png : One of input images.
	- flow1.flo : Floating-point 2D flow map (from image1 to image2). Not required.
	- flow2.flo : Floating-point 2D flow map (from image2 to image1). Not required.
	- mask1.png : Foreground mask for image1. Not required.
	- mask2.png : Foreground mask for image2. Not required.

/feature (output)
	- Output files of feature extraction.

/out (output)
	- flow1.flo : Estimated flow map (from image1 to image2).
	- flow2.flo : Estimated flow map (from image2 to image1).
	- mask1.png : Estimated foreground mask for image1.
	- mask2.png : Estimated foreground mask for image2.
	- options.txt : List of option settings. (Some of them are explained below).
	- summary_f1.png : Warped image results at each hierarchical layer. (Flip between f1 and f2 to verify alignment).
	- summary_f2.png : Original images
	- summary_f3.png : Warped image results with segmentation at each hierarchical layer.
	- summary1.png : Results of image1. See explanations below.
	- summary2.png : Results of image2. See explanations below.

When -doPerPixelRefinement is on (1), there will be four more files.
Files (flow and mask) with _01 _00 are results before and after the final refinement, respectively.


---------------
Visualization :
---------------
Summary images (summary1 and summary2) show results of finest to coarsest layers from left to right columns.
1st row : Superpixel hierarchy.
2nd row : Estimated flow maps.
3rd row : Ground truth flow maps (if provided in the data directory).
4th row : End-point flow error maps (if ground truth is provided in the data directory).
5th row : Estimated warped images. 
6th row : Estimated segmentation results.
7th row : Ground truth segmentation (if provided in the data directory).


------------------
Option parameters :
------------------
Options of the main algorithm (CosegMatching.exe):
	-doPerPixelRefinement [1 or 0] : Use the finest pixel layer or not. This refinement takes time.
	-featureType          [string] : Extension of feature files. By changing this you can use your own features. See below.
	-matchingMetric       [1 or 2] : Norm of Equation 4 in the paper.
	-matchingEngW         [float]  : lambda_flo in Equation 2.
	-matchingEngOcc       [float]  : lambda_occ in Equation 3.
	-matchingEngTrunc     [float]  : tau_D in Quation 4.

Options of feature extraction (DenseFeatureExtract.exe):
	-maxImageDimension    [int]    : The maximum image dimension (mostly width) is resized to this value (default 512).
	-forceImageDimension  [1 or 0] : If 0, images are resized only when they are smaller than the specified size.

--------------------
Feature file format :
--------------------
The feature files (image1.hogc and image2.hogc in /feature) are structured as follows.
The core algorithm only refers the feature vectors of the finest level.
When you make your own feature files,
the image size of the finest level should be the same with the settings of DenseFeatureExtract.


[int32] : Width of the original image.
[int32] : Height of the original image.
[int32] : Number of image pyramid levels.
[int32] : Size of feature patches (not refered in the main algorithm).
[int32] : Dimension of feature vectors (dim).
for ( each pyramid level in coase to fine order )
{
	[int32] : Number of feature vectors in this level.
	[int32] : Width of this level (wk).
	[int32] : Height of this level (hk).
	[float] : Scale of this level.
	
	for ( each feature vector in this level )
	{
		[float] : Normalized x coordinate. (int)(x * wk) is x coodinate in this level.
		[float] : Normalized y coordinate. (int)(y * hk) is y coodinate in this level.
		[float] : Sum of absolute gradient of paches (reliability of this feature). Not refered by default.
		[float array] : Feature vector data (float array[dim]).
	}
}


--------
History:
--------

11/02/2016 v1.0 Released the demonstration binaries.
