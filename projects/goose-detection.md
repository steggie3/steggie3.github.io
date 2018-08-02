---
title: Object Detection with Goose Dataset
permalink: /projects/goose-detection.html
layout: single
classes: wide
author_profile: true
---

I based this project on the [darkflow](https://github.com/thtrieu/darkflow) implementation of the [YOLOv2](https://arxiv.org/abs/1612.08242) object detection algorithm. The output of the algorithm are bounding boxes of each detected object and their associated confidence scores. The input are images or videos.

Both *darkflow* and *YOLOv2* provide pretrained weights for several neural network architectures. By loading the pretrained weights, I was already able to detect things. For example, I used the *tiny-yolo-voc* architecture (a lightweight, 20-class object detection network) and it was able to detect a cat in an image:

<figure>
  <img src="{{site.url}}/projects/goose-detection/cat.jpg" alt="cat.jpg"/>
  <figcaption>YOLO detects my neighbor's cat sitting on a bench, draws a bounding box for it, and marks it as "cat."</figcaption>
</figure>

Then, I wanted to apply transfer learning, fine-tuning the weights by providing training data from a different source. I first tried the [raccoon dataset](https://github.com/datitran/raccoon_dataset). Raccoons are not one of the 20 object classes that *tiny-yolo-voc* was trained on, so training the network to recognize raccoons would be a good transfer learning application. However, it turned out that with only 200 images and great variations in raccoon poses, lighting conditions, background complexity, etc., YOLO is not doing a great job after fine-tuning. It was able to detect most raccoons in the training set, but failed to detect every raccoon in the testing set, showing significant overfitting.

<figure>
  <img src="{{site.url}}/projects/goose-detection/raccoon-overfit.png" alt="raccoon-overfit.png"/>
  <figcaption>YOLO detects most of the raccoons in the training set (numbered from 1 to 160) but none in the testing set (numbered from 161 to 200).</figcaption>
</figure>





