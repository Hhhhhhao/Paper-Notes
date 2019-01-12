## Very DEep Convolutional Networks for large-sacle image recognition



### Abstract

------

- investigate the effect of the depth of CNN on the accuracy of large-scale image recognition.
- Fix other parameters, only change the depth of a CNN using small kernels i.e. $3 \times 3$



### Model Details

------

#### Architecture

- the input to the ConvNet is a fixed-size $224 \times 224$ image

- The only pre-processing conducted is subtracting the mean RGB value, comouted on the training set, from each pixel

- use small filters i.e. $3 \times 3$; it's easy to see that a stack of two $3 \times 3$ conv. layers (without spatial pooling in between) has an effective receptive field of $5 \times 5$; three has effective filed of $7 \times 7$. **The use of small kernels, firstly, incorporate more non-linearitiess in between rather than a single one, which makes the decision function more discriminative; secondly, decreses the number of parameters.** For example, assuming that both the input and the output of a three-layer $3 \times 3$ convolution stack has C channels, the stack is parametrised by $3(3^2C^) = 27C^2$ weights (without bias); at the same time, a single $7 \times 7$ conv. lyer requires $7^2C^2 = 49C^2$ parameters, i.e. 81% more. ==This can be seen as imposing a regularisation on $7 \times 7$ conv. Filters, forcing them to have a decomposition through the $3 \times 3$ filters.==

- ==In one configuration of, $1 \times 1$ filters are also used, which can be seen as a linear transformation of the input channels (followed by non-linearity).== The imposing the $1 \times 1$ conv. layers (configuration C) is a way to increase the non-linearity of the decision function without affecting the receptive fields of the conv. layers. $1 \times 1​$ conv. layers has been utilzed in [network in network](https://arxiv.org/abs/1312.4400)

- convolution stride is fixed to be 1 pixel

- same padding is used throughout

- spatial pooling is carried out by 5 max-pooling layers, which follow some conv. layers;

   Max-polling is performed over a $2 \times 2$ window, with stride 2.

- A stack of conv layers is followed by 3 Fully-Connected (FC) layers: the first 2 have 4096 units each, and the thrid performs 1000 classification. followed by softmax activation.

- All hidden layers use ReLU activations.

- **Local Response Normalisation** proposed in ALexNet does not improve the performance, but leads to increase of memory consumption and computation time.

This paper investigate several ConvNet confirgurations, from 11 weights layers (8 conv, 3 fc) to 19 weighted layers The width of network are rather small (channels), starting from 64 in the first layer and then increasing by a factor of 2 after each max-pooling layer, until it reaches 512.

![image_1](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/1.png)



### Training & Testing Detials

------

#### Training details basically follows AlexNet.

- Batch sizie 256
- SGD with momentum 0.9
- weight decay 0.0005, learning rate intialized at 0.01 and reduce by deviding 10 (3 times in total)
- 70 epochs, 370K iterations
- Data Processing:
  - Randomly crop from rescaled image
  - random horizontal flipping
  - random RGB color shift (same in AlexNet)

#### Datasets

- ILSVRC-2012
- ILSVRC-2014



### Results

------

#### Single Scale Evalutaion

![2](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/2.png)

- First we notice LRN doesn't help with performance
- Classification error decreases with the increased Conv depth
- Notably, C (contains three $1 \times 1$ conv. layers), performs worse than D (uses $3 \times 3$  layers), indicating that while additional non-linearity does help (C is better than B), it is also important to capture spatial context by using conv. layers with non-trivial receptive fields.
- Deep nets with small filters outperforms shallow net with larger filters.
- Sclae jittering at training time [256, 512] leads to significantly better results than training on fixed sizes. ==Training set augmentation by scale jettering is indeed helpful for capturing multi-scale image statistics.==



#### Multi-Scale Evalutation

![3](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/3.png)

- scale jittering at testing test-time leads to better performance
- deepest configurations perform the bset



#### Multi-Crop Evalutaion

![4](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/4.png)

- evalutaion with multi-crop
- Assess the complementarity of the 2 evalutaion techniques by averageing their softmax outputs

#### ConvNet Fusion

![6](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/6.png)

combine the outputs of several models by averaging their soft-max posteriors, which improves the performance due to complementarity of the models

#### Comparison with state-of-the-art in ILSVRC classifciation

![7](/Users/leon/Projects/paper-notes/Very-Deep-Convolutional-Networks-for-Large-Scale-Image-Recognization/res/7.png)



### Conclusion

- Depth is beneficial for the classification accuracy (demonstrated wrong by resnet)
- models generalised well to a wide range of tasks and datasets.