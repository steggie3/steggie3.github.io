---
title: Object Detection with Goose Dataset
permalink: /projects/goose-detection.html
layout: single
classes: wide
author_profile: true
teaser: /assets/images/goose-detection/goose_iou.png
---

I based this project on the [darkflow](https://github.com/thtrieu/darkflow){:target="_blank"} implementation of the [YOLOv2](https://arxiv.org/abs/1612.08242){:target="_blank"} object detection algorithm. The output of the algorithm are bounding boxes of each detected object and their associated confidence scores. The input are images or videos.

Both *darkflow* and *YOLOv2* provide pretrained weights for several neural network architectures. By loading the pretrained weights, I was already able to detect things. For example, I used the *tiny-yolo-voc* architecture (a lightweight, 20-class object detection network) and it was able to detect a cat in an image:

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/cat.jpg" alt="cat.jpg"/>
  <figcaption>YOLO detects my neighbor's cat sitting on a bench, draws a bounding box for it, and marks it as "cat."</figcaption>
</figure>

Then, I wanted to apply transfer learning, fine-tuning the weights by providing training data from a different source. I first tried the [raccoon dataset](https://github.com/datitran/raccoon_dataset){:target="_blank"}. Raccoons are not one of the 20 object classes that *tiny-yolo-voc* was trained on, so training the network to recognize raccoons would be a good transfer learning application. However, it turned out that with only 200 images and great variations in raccoon poses, lighting conditions, background complexity, etc., YOLO is not doing a great job after fine-tuning. It was able to detect most raccoons in the training set, but failed to detect any raccoon in the testing set, showing significant overfitting.

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/raccoon_overfit.png" alt="raccoon_overfit.png"/>
  <figcaption>YOLO detects most of the raccoons in the training set (numbered from 1 to 160) but none in the testing set (numbered from 161 to 200).</figcaption>
</figure>

I've always wanted to try some computer vision algorithms on Canada geese because they are my favorite animal. For example, counting geese in one image. However, from the experience with the raccoon dataset, I realized that the amount of accessible data and computation power would be a bottleneck for me, so I thought I should start with something simpler. For example, locating only the goose's head, which as a strikingly high-contrast pattern, rather than the goose's entire body. As a result, I put together 1,000 goose mugshots from my photo collection, labeled the head location with an open-source labelling tool, [labelImg](https://github.com/tzutalin/labelImg){:target="_blank"}, and made a [dataset](https://github.com/steggie3/goose-dataset){:target="_blank"} to experiment on.

First, I used the pretrained *tiny-yolo-voc* model to detect the geese in my dataset. Geese, like raccoons, aren't one of the 20 pretrained classes. The model is able to recognize some of the geese as *birds*. Interestingly, the bounding boxes are usually put around only the heads, not the entire visible body. It could be because that most birds do not have long necks like geese do.

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/goose_pretrained.png" alt="goose_pretrained.png"/>
  <figcaption>A pretrained YOLO detects some of the geese as birds.</figcaption>
</figure>

Next, I fine-tuned the model using the goose dataset. In addition to adapting the neural network to 1-class, I also recomputed the ideal sizes of anchor boxes by [applying the K-means algorithm on the bounding boxes of the dataset](https://github.com/steggie3/goose-dataset/blob/master/notebooks/compute_anchor_box.ipynb){:target="_blank"} and picking the results from k=5. I separated the 1,000 images into a training set of 800 images and a testing set of 200. 

The model is able to recognize a lot of geese after training for just a few epochs. The center points of the bounding boxes are already pretty accurate, but the bounding boxes have much to improve. This is because YOLO's cost function penalizes on inaccurate center points more than inaccurate bounding boxes, so the center point accuracy is optimized first. Note that some goose heads are marked with two bounding boxes. This is due to the nature of YOLO. Each detection grid would output one bounding box. Among bounding boxes with a certain amount of overlapping (measured by Intertection-Over-Union, IOU), only one would be selected as the final bounding box. In images with two bounding boxes, the two boxes are of significantly different sizes albeit sharing the same center points. As a result, the IOU is not high enough for them to eliminate the other.

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/preliminary.jpg" alt="preliminary.jpg"/>
  <figcaption>Preliminary results of fine-tuning on the testing set. The center points of the bounding boxes are already pretty accurate.</figcaption>
</figure>

After training for 500 epochs, the quality of the bounding boxes improved a lot. Both the center points and the bounding boxes look good for the ones that were detected. However, some geese are still not recognized.

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/goose_final.png" alt="goose_final.png"/>
  <figcaption>Final results of fine-tuning on the testing set. Both the center points and the bounding boxes are good for the ones that were detected.</figcaption>
</figure>

To get a more quantitative evaluation on the results, I followed the YOLO paper and used the *mean average precision (mAP)* as the metric. I used [this implementation of mAP calcuation](https://github.com/Cartucho/mAP){:target="_blank"} to analyze my predictions. The mAP is 85.50%. Out of the 200 geese in the testing set, 171 were detected, and there were no false positives. For the 177 detected geese, the predicted bounding boxes are also visually close to the ground truth bounding boxes.

<figure>
  <img src="{{site.url}}/assets/images/goose-detection/goose_iou.png" alt="goose_iou.png"/>
  <figcaption>Bounding boxes of detected geese. Blue is the ground truth, and green is predicted.</figcaption>
</figure>