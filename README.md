# Adversarial Single Image Depth Estimation  

> This work was done as the Final Project of CMPT 726 Fall 2019 (Instructor: [Greg Mori](https://www.cs.sfu.ca/~mori/)).

Contributors:
Parham Yassini, Taher Ahmadi, Elnaz Mehrzadeh, Dorsa Dadjoo*, Fatemeh Hasiri

For a quick overview of the project please check the [project poster](./docs/poster.pdf).
For a more detailed analysis please checkout the [projet report](./docs/report.pdf).
 
### Usage guide
Download the trained models and put in the root of project:
[Depth estimation networks](https://drive.google.com/file/d/1IJ8XvPOb3k-aEg0UX5Zp96sA-8C_PthB/view?usp=sharing) <br>
Download the data and put it in the the following structure:
[NYU-v2 dataset](https://drive.google.com/file/d/1WoOZOBpOWfmwe7bknWS5PMUCLBPFKTOw/view?usp=sharing) <br>
.(project root)/data/<br>
		├── nyu2_test<br>
		├── nyu2_test.csv<br>
		├── nyu2_train<br>
		└── nyu2_train.csv<br>

+ **Demo**
  python demo.py<br>
+ **Test**
  python test.py<br>
+ **Training**
  python train.py<br>

## Introduction
Estimating depth from 2D images is a crucial step in many applications such as scene reconstruction, 3D object recognition, segmentation, and detection. The problem can be framed as: given a single RGB image as input, predict a depth map for each pixel.

Most Existing methods (1) Suffer from loss of spatial resolution in the estimated depth maps and (2) Have distorted and blurry reconstruction of object boundaries. Goal of this project is to investigate the impact of exploiting adversarial loss and different complementary loss functions for improving the mentioned shortcomings

### Contributions 
We extend the baseline method of [Hu et al. (2018)](https://arxiv.org/pdf/1803.08673.pdf)  by adding a discriminator network and introducing two new loss functions. The discriminator network (D) is trained using ground truth depth maps from the dataset and reconstructed depth maps from the depth estimation module (generator). The discriminator forces the depth estimator to generate depth maps that are more similar to the real depth maps.

Also, we use different the loss functions to (1) compare the structural similarity of the reconstructed depth map and ground truth depth and (2) penalize the depth error on nearby objects depth more than distant objects.  

##  Network Architecture

![Diagram of the proposed network architecture](../docs/net-arch.png)

Given an input image, the encoder extracts multi-scale features. The decoder converts the last 1/32 scale feature to get a 1/2 scale feature. Each of the multi-scale features is up-scaled to 1/2 scale and fused by the multi-scale feature fusion module (MFF). The outputs of D and MFF and are refined by the refinement module ( R ) to obtain the final depth map. 

### Descrimnator Network
A discriminator network is added to differentiate between fake and real depth maps. The depth map generator tries to fool the discriminator with generated depth maps while the discriminator is fed with the ground truth depth maps learns to distinguish fake depth maps from the real ones. Accordingly, the adversarial loss would force the depth map generator to produce outputs that follow the natural distribution of the depth maps in the data set.

> For loss functions and intuition behind them, please refer to the project poster or report.
> 
## Results
We evaluate our model using the NYU-Depth V2 dataset. The dataset consists of a variety of indoor scenes from which we used 50K samples for the training phase and 1K samples for testing. 

The data augmentation is identical to the baseline method: Flip, Rotation and Color Jitter were applied on each pair in the dataset.

Due to the time limit, we trained our network 5 epochs instead of 20 epochs that the baseline method used. We also, trained the baseline network for 5 epochs to perform a fair comparison between results. The initial learning rate is 0.0001, and reduce it to 10% for every epoch after the second iteration.

![Example of the output results from test dataset](../docs/eval-example.png)

Beside test-cases from the dataset, we evaluate the model on new indoor images taken using smartphone camera. Since ground truth values for depth is not available, it is only used for visual comparison. As seen in the figure below, our results show improved object boundries.

![Visual comparison of the result for the purposed method](../docs/eval-visual.png)

> For quantitative comparison please refor to the report document.
