# DCGAN

------

[UnAlec Radford et al. *Unsupervised Representation Learning With Deep Convolutional Generative Adversarial Networks*](http://arxiv.org/abs/1511.06434)

### Abstract & Intro

------

- Introduce DCGANs, which have certain architectural constraints
- Demonstrate that they are strong for unsupervised learning
- Learning resuable feature representations from unlabled datasets, which can be used on a variety of supervised learning tasks such as image classification：***training DCGANs to capture good image representations and later resuing parts of the generator and discriminator networks as feature extractors for supervised tasks***
- The main contributions of this paper:
  1. propose and evaluate a set of constraints on the architecture topology of Convolutional GANs that make them stable to train in most settings, namely DCGANs
  2. use the trained discriminators for image classification tasks, showing competitive performance with other upsupervised algorithms
  3. visualize filters learnt by GANs and show that specific filters have learned to  draw specific objects
  4. show that the generators have interesting vector arithmetic properties allowing for easy manipulation of many semantic qualities of generatred samples ?



### Approach and Model Architecture

------

Core idea of DCGANs is adopting and modifying three changes to CNN architectures:

1. Use fully convolutional net, which replace spatial pooling functions with strided convolutions, allowing the network to learn its own spatial downsampling.
2. Eliminating fully connected layers on the top of convolutional features.
3. Batch Normalization, whichi stabilizes learning by normalizaing the input to each unit to have zero mean and unit variance. Directly appliying batchnorm to all layers, however, results in sample oscillation and model instability. This was avoided by not applying batchnorm to the generator output layer and the discriminator input layer.

Additional tricks are:

1. ReLU activation for the generator, except for the last layer, which use tanh or sigmoid (depends on data normalization).
2. <u>A bounded activation allows the model to learn more quickly to saturate and cover the color space of the training distribution.</u>
3. Leaky ReLU is used for discriminator.



### Detials of Adversarial Training

------

#### Training details

- DCGANs is trained on three datasets: Large-scale Scene Unverstanding (LSUN), ImageNet-1k and a newly assenbled Faces dataset.
- Only rescale is applied to scale the images into the range of tanh activation function [-1, 1].
- All weights are initialized from a zero-centered Normal distribution with standard deviation 0.002.
- The slope of the leak in LeakyRuLu is 0.2.
- Adam optimizer with a learning rate of 0.0002 is used. Additionally, leaving the momentum term $\beta_1$ at the suggested value of 0.9 resulted in training oscillation and instability while reducing it to 0.5 helped stabilize training.

Architecture

![image-20180725155702775](images/dcgan1.png)



### Evaluation

------

#### Classifying CIFAR-10 Using GANs As a Freture Extractor

One common techinique for evaluating the quality of unsupervised representation learning algorithms is to apply them as a feature extractor on supervised datasets and evaluate the performance of linear models fitted on top of these features

To evaluate the quality of the representations learned by DCGANs for supervised tasks, we train on ImageNet-1k and then use the discriminator's convolutional features from all layers, maxpooling each layers representation to produce a $4 \times 4$ spatial grid. These features are then flattened and concatenated to form a 28672 dimensional vector and a regularized linear **L2-SVM** classifier is trained on top of them.

![image-20180725160600485](images/dcgan2.png)



### Visualizing The Internals of The Networks

------

#### Walking in the Latent Space

Walking on the manifold that is learnt can usually tell us about signs of memorization (if there are sharp transitions) and about the way in which the space is heirachically collapsed.

![image-20180725161034592](images/dcgan3.png)

#### Visualizing The Discriminator Features

Using guided backpropagation as proposed by (Sprin, 2014), we show that the features learnt by the discriminator activate on typical parts of a bedroom, like beds and windows.

![image-20180725161309864](images/dcgan4.png)



Manipulating The Generator Representation

See paper for more detail, they explore what feature the generator has leanrt. Also interesting vector arithmetic on face samples has beed done.