## ImageNet Classification with Deep Convolutional Neural Networks

### Abstract

------

The authors proposed a large, deep convolutional network that can classify ImageNet images in a high accuracy. (2012)

### Architecture and Detials

------

1. #### Architecture

   Input size: $224 \times 224 \times 3$

   1st conv layer: kernel size of (11, 11), with 96 filters and a stride of 4

   followed by maxpooling

   2nd conv layer: kernel size of 5, filters 256, followed by maxpooling.

   3rd conv layer: kernel size 3, filters 384.

   4th conv layer: kernel size 3, filters 384.

   5th conv layer: kernel size 3, filters 256, followed by maxpooling.

   6th fully connected layer: 4096 neutrons, followed by dropout

   7th fully connected layer: 4096 neutrons, followed by dropout

   8th fully connected layer: 1000 neutrons, softmax activate

   #### ![model architecture](./res/alexnet1.jpg)

2. #### ReLU Nonlinearity

   sigmoid and tanh activations are saturating nonlinearities so the training time of using them are much slower than using the non-saturating nonlinearities such as ReLU

   ![ReLU](./res/alexnet2.jpg)

3. #### Local Response Normalization

   ![Response norm](./res/alexnet3.jpg)

4. #### Overlapping Pooling

5. #### Data augmentation

   (1) extract 224 patches from 256 images and their horizontal reflection

   (2) altering the intensities of the RGB channels in training images.

6. #### Training Details

   batch size of 128, learning rate initialize at 0.01 with a decay rate



### Results

------

![results](./res/alexnet4.jpg)