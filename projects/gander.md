---
title: The GANder Project
permalink: /projects/gander.html
layout: single
classes: wide
author_profile: true
---

After building the [Goose Dataset](https://github.com/steggie3/goose-dataset){:target="_blank"} and using it to run [object detection experiments](/projects/goose-detection.html){:target="_blank"}, I thought of another application with the dataset: generating artificial geese with Generative Adversarial Networks (GANs).

## GAN

The idea behind GAN is to have a *generator* that generates artificial images and a *discriminator* that distinguishes fake/artificial images from real images in the training set. In each learning iteration, the generator learns to generate better images that cheats the discriminator, and the discriminator learns better criteria to detect fake images. 

<figure>
  <img src="https://deeplearning4j.org/img/GANs.png" alt="GANs.png"/>
  <figcaption>Architecture of a generative adversarial network. (Source: DL4J)</figcaption>
</figure>

Many GAN implementations, including the [original paper](https://arxiv.org/pdf/1406.2661.pdf){:target="_blank"}, tested on the MNIST dataset. The images in MNIST are grayscale and are of size 28 x 28. To make the Goose Dataset more similar to this and hopefully achieve similarly high-quality results, I cropped the goose heads from the images, turned them grayscale, and resized them to 50 x 28.

<figure>
  <img src="{{site.url}}/assets/images/gander/cropped.png" alt="cropped.png"/>
  <figcaption>Sample cropped images from the Goose Dataset. Some of the images became a bit disproportioned after resizing, for example, goose-mugshot-0012 and goose-mugshot-0022.</figcaption>
</figure>

I based my experiments on [this Keras implementation of GAN](https://github.com/eriklindernoren/Keras-GAN){:target="_blank"}. The vanilla GAN has a simple architecture and is very fast to train on a personal computer without a GPU. It took me less than 2 hours to train for 60,000 epochs. The loss and accuracy of the discriminator and the loss of the generator are displayed for each epoch, and a collection of 25 generated images is saved for every 200 epochs.

<figure>
  <img style="width:409px ! important;" src="{{site.url}}/assets/images/gander/loss.png" alt="loss.png"/>
  <figcaption>The loss and accuracy of the discriminator (D) and the loss of the generator (G) are displayed for each epoch (per line).</figcaption>
</figure>

From the generated images, we can see the progress of the learning process of GAN.

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/0.png" alt="0.png"/>
  <figcaption>Epoch 0. All images are initialized as random noises.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/400.png" alt="400.png"/>
  <figcaption>Epoch 400. We can see some very rough shapes of geese. Because my dataset contains goose heads from both the left and the right side of the geese, some images look like there are two goose heads overlapping.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/2000.png" alt="2000.png"/>
  <figcaption>Epoch 2,000. Goose heads are becoming more clear.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/5000.png" alt="5000.png"/>
  <figcaption>Epoch 5,000. The outlines of the geese and the cheek patterns are more defined now.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/10000.png" alt="10000.png"/>
  <figcaption>Epoch 10,000. Cheek patterns look almost realistic now, but the beaks are still blurry. Note that sometimes we still get a couple of noisy images. It may have come from overfitting of some neurons of the generator.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/20000.png" alt="20000.png"/>
  <figcaption>Epoch 20,000. The beaks are more well-defined now.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/59800.png" alt="59800.png"/>
  <figcaption>Epoch 59,800. No significant improvement since Epoch 20,000, but faint eyes can now be seen in a small number of images.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/60000.png" alt="60000.png"/>
  <figcaption>Epoch 60,000. I still get some noisy images.</figcaption>
</figure>

Next, I increased the number of channels to 3 to include RGB colors instead of using grayscale. The results are not good. Even after 60,000 epochs, only noises were generated. 

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan_rgb/60000.png" alt="60000.png"/>
  <figcaption>Epoch 60,000, RGB. Only noises were generated.</figcaption>
</figure>

I tried resizing the images to be slightly smaller, decreasing the width from 50 pixels to 42. Then, goose faces were learned, although a bit disproportioned due to resizing. A mostly green and blue background is also learned, likely due to the fact that most of the backgrounds in the dataset are either grass or water.

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan_rgb/60000.png" alt="60000.png"/>
  <figcaption>Epoch 30,000, RGB. We got some goose faces with mostly recognizable cheek patterns and beaks. The background is mostly green and blue.</figcaption>
</figure>

Analyzing the above results, I can see a few issues:
- Some groups of images are repeated and are almost visually identical. This is called *mode collapse*. Here is a short [article](http://aiden.nibali.org/blog/2017-01-18-mode-collapse-gans/){:target="_blank"} explaining mode collapse.
- Noises. The quality of the final results resemble some of the earliest photos from the 19th century (especially the grayscale ones). There are a lot of noises, and the edges are not very well-defined. Moreover, some images are still almost pure noises even after 60,000 epochs of training.

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/gan/59800_mode_collapse.png" alt="59800_mode_collapse.png"/>
  <figcaption>Epoch 59,800. Some groups of images (marked with boxes of the same color) are almost visually identical, showing mode collapse.</figcaption>
</figure>

After some survey, I found that these are common issues with GAN, and people have been proposing new approaches to address them.

## WGAN

Wasserstein GAN (WGAN) is one improvement work. In the [WGAN paper](https://arxiv.org/pdf/1701.07875.pdf){:target="_blank"}, the authors put emphasis on WGAN's ability to overcome mode collapse. They showed side-by-side comparison with GAN in the experimental results. In one example, WGAN is also able to learn where GAN failed to learn anything but noises. These look like the improvement I need for this project as well. These improvements are achieved by replacing the Jensen–Shannon (J-S) divergence that the original GAN discriminator optimizes for with the *Wasserstein distance* or *Earth Mover (EM) distance*. The original GAN discriminator treats a very poor fake image and a slightly improved fake image all the same, so it does not reward small, incremental improvement in the quality of the generator. On the contrary, the *Wasserstein distance* is able to distinguish incremental improvement and encourages the generator to make such improvement.  

<figure>
  <img style="width:898px ! important;" src="{{site.url}}/assets/images/gander/wgan_exp_results.png" alt="wgan_exp_results.png"/>
  <figcaption>Some experimental results in the WGAN paper, with side-by-side comparison with GAN. It shows that WGAN addresses common issues encountered in GAN.</figcaption>
</figure>

I based my experiments on [the same repo as above](https://github.com/eriklindernoren/Keras-GAN){:target="_blank"}. I slightly adapted the neural network architecture to accommodate for rectangular rather than square images, and then ran the experiments with grayscale cropped images. WGAN is more computationally expensive than the vanilla GAN, and it took me 8 hours to run 30,000 epochs on a personal computer with GPU. Here are the results.

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_0.png" alt="goose_0.png"/>
  <figcaption>Epoch 0. All images are initialized as random noises. The random noises look more "cloudy" than the ones from GAN.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_1000.png" alt="goose_1000.png"/>
  <figcaption>Epoch 1,000. Some black and white patches are generated.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_3000.png" alt="goose_3000.png"/>
  <figcaption>Epoch 3,000. The ratio of black and white patches is close to that of a goose.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_8000.png" alt="goose_8000.png"/>
  <figcaption>Epoch 8,000. We can see something that resembles a goose. Some images have two goose heads of opposite directions overlapping.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_15000.png" alt="goose_15000.png"/>
  <figcaption>Epoch 15,000. Getting more good-looking geese. Their cheek patterns and the edges of the head are quite well-defined compared to those generated by GAN.</figcaption>
</figure>

<figure>
  <img style="width:640px ! important;" src="{{site.url}}/assets/images/gander/wgan/goose_30000.png" alt="goose_30000.png"/>
  <figcaption>Epoch 30,000. Most of the cheek patterns are clear, but I still got quite a few overlapping geese or ill-shaped geese.</figcaption>
</figure>

## VAE-GAN

Another adaptation of GAN is Variational Autoencoder (VAE) GAN. The main idea behind VAE-GAN is to recognize that the generator part of GAN is equivalent to the decoder part of an autoencoder. A VAE encodes the original data into two components, mean and variance. This helps learning the similarities in data and produces higher-quality images.

I based my experiments on [another Keras implementation of GAN](https://github.com/tatsy/keras-generative). This time, I did not change the network architecture. Instead, I padded my rectangular images with black strips on the top and the bottom. These experiments took the longest to run, taking a few days for just over 1,000 epochs on a personal computer with GPU. Here are the results.

<figure>
  <img style="width:2000px ! important;" src="{{site.url}}/assets/images/gander/vaegan/epoch_0000.png" alt="epoch_0000.png"/>
  <figcaption>Epoch 0. Images are initialized as all gray with some noises.</figcaption>
</figure>

<figure>
  <img style="width:2000px ! important;" src="{{site.url}}/assets/images/gander/vaegan/epoch_0050.png" alt="epoch_0050.png"/>
  <figcaption>Epoch 50. The bottom black strip is learned. If you look very closely, you can also see very faint goose heads with cheek patterns.</figcaption>
</figure>

<figure>
  <img style="width:2000px ! important;" src="{{site.url}}/assets/images/gander/vaegan/epoch_0250.png" alt="epoch_0250.png"/>
  <figcaption>Epoch 250. The contrast is getting higher.</figcaption>
</figure>

<figure>
  <img style="width:2000px ! important;" src="{{site.url}}/assets/images/gander/vaegan/epoch_0700.png" alt="epoch_0700.png"/>
  <figcaption>Epoch 700. We can see some goose heads with green backgrounds between top and bottom black strips.</figcaption>
</figure>

<figure>
  <img style="width:2000px ! important;" src="{{site.url}}/assets/images/gander/vaegan/epoch_1050.png" alt="epoch_1050.png"/>
  <figcaption>Epoch 1,050. Among the well-formed goose faces, the images are more clear and the edges well-defined.</figcaption>
</figure>

## Future Work Ideas
GAN is sensitive to hyperparameters. The hyperparameters and neural network architecture can be further explored to find the optimal setting to generate goose faces. Having access to more computation power would also help.