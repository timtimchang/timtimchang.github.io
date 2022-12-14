##γDocTr: Document Image Transformer for Geometric Unwarping and Illumination Correction

οΌinstitution: CAS Key Laboratory of Technology in GIPAS
author: Hao Feng
conference: ACM MM 2021
github: https://link.zhihu.com/?target=https%3A//github.com/fh2019ustc/DocTr

![](2022-09-06-10-07-11.png)
> Figure 1: Qualitative rectified results of our Document Image Transformer (DocTr). The top row shows the distorted document images. The second row shows the rectified results after geometric unwarping and illumination correction.

### ABSTRACT
- DocTr consists of a **geometric unwarping transformer** and an **illumination correction transformer**. 
- By setting a set of **learned query embedding**, the geometric unwarping transformer captures the global context of the document image by self-attention mechanism and decodes the pixel-wise displacement solution to correct the geometric distortion. 
- After geometric unwarping, our illumination correction transformer further **removes the shading artifacts** to improve the visual quality and OCR accuracy.

### APPROACH
![](2022-09-06-10-26-30.png)
> Figure 2: An overview of Document Image Transformer (DocTr). It consists of two main components: a geometric unwarping transformer and an illumination correction transformer.
#### Geometric Unwarping Transformer
- As shown in Figure 2, given an image $π°_{π·} β R^{π»Γπ Γ3}$ , we first downsample it and get the image $π°_{π}β R^{π»+{0}Γπ_{0}ΓπΆ_{0}}$ , where $π»_{0} = π_{0} = 288$ in our method and $πΆ_{0} = 3$ is the number of RGB channels.
- Then, $π°_{π}$ is fed into the preprocessing module to get the background-excluded image $π°_{π}$ which is injected into geometric unwarping transformer to predict a backward mapping field $π_{π}$.
- With $π_{π΅}$, the rectified document image $π°_{π} β R^{π»Γπ Γ3}$ can be obtained by warping operation.

**Preprocessing**
- a **light semantic segmentation network** is utilized to predict the confidence map of the foreground document, which is further binarized with a threshold $\tau$ to obtain the binary document region mask $π΄_{π°_{d}}$.


**Transformer Encoder**
![](2022-09-06-13-52-58.png)
> Figure 3: Architecture of the transformer encoder-decoder.
- features are then extracted from $π°_{\theta}$ using a convolutional module $πΊ_{\theta}$ that consists of 6 residual blocks.

- $G_{\theta}$ downsamples the feature maps at $\frac{1}{2}$ resolution every two blocks and output features $π_{g}$.

- we flatten $π_{π}$ into a sequence of 2D features, i.e., $π_{π } β R^{π_{π}Γπ_{π}}$ , where $π_{π} = \frac{π»_{0}}{8} Γ \frac{π_{0}}{8}$ is the number of patches.

- to maintain the position information in the process, we add learnable 2D position embedding $π¬_{π} β R^{π_{π}Γπ_{π}}$ following, which is consistent to different input images.

- As shown in Fig 4, we train a learnable module to perform upsampling on the decoded features and obtain high-resolution predictions.


![](2022-09-06-14-10-27.png)
> Figure 4: Illustration of the tail network for unwarping.
**Loss Function**
- The training loss for our geometric unwarping transformer is defined as the $πΏ_{1}$ distance between the predicted backward mapping $π_{π}$ and ground truth $π_{ππ‘}$ as follows,
![](2022-09-06-14-15-18.png)


#### Illumination Correction Transformer
- As shown in Figure 2, given the unwarped image π°π β R π»Γπ Γ3 , we first crop π°π into patches with a overlap of 12.5% (each patch at 128Γ128 resolution).

**Transformer Encoder-Decoder**
- Like Transformer in Geometric Unwarping, reshape the patch feature $π_{π|}$ into a sequence of flattened π Γ π mini-patches.
- $f_{π π} β R^{π_{π}Γπ^{β²}_{π}}$ , where $π_{π} = \frac{π»_{π}Γπ_{π}}{π^{2}}$ is the number of mini-patches on the input image patch, $π^{β²}_{π} = π_{π} Γ π^{2}$ is the number of channels of transformer input. 
- Note that in our method, $π_{π}$ is set to 16, and $π$ is set to 4.

**Loss Function**
We optimize the illumination correction transformer by minimizing the $πΏ_{1}$ distance and the VGG loss between the estimated patch image $πΌ_{π}$ and ground truth image $πΌ_{ππ‘}$ as follows,
![](2022-09-06-14-21-02.png)
where $\alpha$ is the weight of the VGG loss and $π (Β·)$ denotes the VGG Network.

### DATASET
- We train the geometric unwarping transformer on Doc3D dataset, following DewrapNet.
- our illumination correction transformer is optimized using the DRIC dataset.
- We evaluate the performance of our DocTr on Doc3D, DRIC, and DocUNet benchmark

###  EXPERIMENTS
![](2022-09-06-14-46-38.png)
![](2022-09-06-14-45-52.png)

![](2022-09-06-14-47-21.png)
> Figure 5: Qualitative comparison with state-of-the-art methods. Column 1: original distorted images, column 2: results of DewarpNet [7], column 3: results of [41], column 4: ours. The comparison of rectified boundaries and text lines are highlighted by yellow arrows and cropped text, respectively.

![](2022-09-06-14-47-43.png)
> Figure 6: Qualitative comparison with illumination correction methods DewarpNet-ILL [7] and DRIC-ILL [18] on unwarped results of DewarpNet [7] and GeoTr, respectively

![](2022-09-06-14-48-14.png)
> Figure 7: Robustness of DocTr in terms of viewpoint. The top row shows the distorted document images taken from different viewpoints. The second row shows the corresponding rectified document images by DocTr.

![](2022-09-06-14-48-43.png)
> Figure 8: Robustness of DocTr in terms of illumination variation and background environment. The top row shows the distorted document images captured in different scenes. The second row shows the rectified document images by DocTr.