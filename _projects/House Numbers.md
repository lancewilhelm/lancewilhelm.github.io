---
layout: page
title: House Number Detection and Classification
description: CS6476 Computer Vision final project, finds and classifies numbers within images using MSER and CNN
img: assets/img/house numbers/house3_tiles.png
importance: 1
category: academic
---

<a href="{{ 'assets/pdf/CS6476___Final_Project.pdf' | relative_url}}" target="_blank" rel="noopener noreferrer" class="float-right">Report <i class="fas fa-file-pdf" style="font-size: 20pt;"></i></a>

## Introduction
Image classification using artificial intelligence techniques, including deep learning, has been a topic of extensive research. Early classification techniques involved methods such as Scale Invariant Feature Transform (SIFT), Histogram of Gradients (HOG), support vector machines (SVM), and K-means clustering. However, the advent of Convolutional Neural Networks (CNN) brought about significant improvements in the ability of computers to quickly and accurately classify images.

#### CNN
Fukushima's "neocognitron" laid the groundwork for CNNs, which was later developed into the first effective CNN, LeNet, by LeCun et al. This model achieved low error rates on the MNIST dataset. In 2012, Krizhevsky et al. developed AlexNet, which marked a breakthrough in CNN development and was used for the ImageNet Large Scale Visual Recognition Challenge (ILSVRC). Since then, modern CNNs like VGG-16 and ResNet have continued to outperform previous benchmarks.

#### Text Detection
Text detection within images has also seen a boost in performance with the advent of CNNs. Before CNNs, text detection and classification methods included thresholding, K-means clustering, and AdaBoost classifier. Matas et al. developed a technique to extract maximally stable extremal regions (MSER) from images, which proved useful in detecting text objects within images. Techniques such as geometric features, stroke width transform (SWT), and Canny edge detection have been used to filter non-text regions.

#### SVHN
This project specifically focuses on digit detection and classification within images. The Street View House Numbers (SVHN) dataset, containing over 600,000 labeled digits from Street View images, is used to train a CNN classifier. Recognizing characters in complex scenes such as photographs is more challenging compared to early datasets like MNIST. The project highlights that there is room for improvement in detecting and classifying digits with horizontal text and no vertical overlapping.

## Approach
This project proposes a two-step approach for detecting and classifying text within images. The first step involves region of interest (ROI) detection using MSER and filtering based on aspect ratio, Canny edges, and non-maximum suppression (NMS). The second step involves CNN digit classification of the detected ROIs, including non-text thresholding.

The MSER detector, implemented using the OpenCV Python package, is tuned to search for individual digits rather than whole words. Detected regions and bounding boxes are filtered based on their aspect ratio, with any region or bounding box having an aspect ratio less than 1 or greater than 3.5 being filtered out. Regions without Canny edges covering more than 10% of their pixels are also filtered out. The remaining bounding boxes are converted to squares and arranged in ascending order by the x-coordinate of the top-left point of the square.

The digit classification CNN is trained on the entire SVHN dataset, including additional images provided. The training set is split into a training and validation set to evaluate model performance during training and enable the selection of the best model. The model with the best accuracy on the validation set is chosen as the final model and evaluated on a separate test dataset. The dataset sizes are: 589,736 for training, 14,652 for validation, and 26,032 for testing. All models are trained using a single Nvidia RTX 3090 Ti GPU.

ROIs detected in the first step are passed through the trained CNN to receive digit classifications. The output for each image is a one-hot vector of length 10, containing likelihood values for each digit. Taking the argmax of the resulting vector indicates the most likely detected digit. Early analysis shows that regions containing text typically have values greater than 5.5, so any ROI with a predicted maximum value less than or equal to 5.5 is filtered out at this stage. The resulting classifications are then converted to strings and returned as the detected string of digits in an image.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/house numbers/fig_1a.jpg" title="Figure 1a" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 1a. Detected squares
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/house numbers/fig_1b.jpg" title="Figure 1b" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 1b. Extracted tiles
</div>

## Results and Analysis

The text ROI detector performed with marginal success. Images containing bolder digits with fewer conflicting blobs of similar size tended to perform better than images with thinner text in more complex scenes. The MSER detector struggles with blurry blobs, and some geometric feature constraints could have filtered out positive ROIs. Cho et al. raise concerns about MSER-based detectors improperly filtering out positive text candidate regions in their paper and relate it to the recall criterion.

Using CLAHE for image normalization helped the algorithm handle changes in contrast and luminance, as demonstrated by the "1" digit tile in figure 1b, which contains shading that splits the image in half. The final results from the training selection of the CNN model used for step 2, digit classification, are given in table 1. The best model came from fine-tuning a pretrained ResNet18 model over 24 epochs, which proved more effective in training efficiency and validation performance compared to VGG16. Fine-tuning a pretrained VGG16 model took approximately six times longer per epoch to train and resulted in lower performance.

Additional models were trained using the original 32 square pixel images in color and grayscale. While their performance was relatively high, they did not outperform the models that transformed the 32 square pixel images into 224-pixel squares. Models that utilized training image augmentation yielded appreciable gains in the model's generalization, as shown by the validation loss curves in figure 2.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/house numbers/fig_2.jpg" title="Figure 2" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 2. Validation loss for CNN models
</div>

The loss function for all models was the cross-entropy loss, which provides an effective probabilistic comparison between classes in a multi-class classification model. The Adam optimizer was chosen for its quick and efficient convergence to a solution. The learning rate was set initially to 3 × 10^(-4) for the Adam optimizer and 1 × 10^(-2) for SGD. A learning rate scheduler was used to decrease the learning rate by a factor of 10 every 7 epochs. The batch size was chosen to maximize GPU memory usage to speed up training.

The selected model yielded an accuracy of 98.421% when evaluated on the test dataset. The confusion matrix in figure 3 shows consistent performance in the classification of digits, with all having an accuracy of 98% or greater, except for the "5" digit with an accuracy of 97.9%. Further training epochs may have yielded greater accuracy, but the decreasing performance increase per epoch would have necessitated many more epochs to see a significant gain.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/house numbers/fig_3.jpg" title="Figure 3" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 3. Confusion matrix for the results of the classification of the test SVHN dataset. The final ResNet18 model achieved an accuracy of 98.421% on the dataset. n = 25621
</div>

## Future Work

Better detection of ROIs can be achieved through deep learning. Two notable models that handle text detection within images are EAST and CRAFT. These models are trained to find text within an image and would improve step 1 of the algorithm presented in this project. Boosting the accuracy of the ROI detection would impact the algorithm's overall performance, as most of the failures occur in the incorrect classification of text regions.

Furthermore, more exploration of CNNs for digit classification could be performed. There exist published models that currently handle the classification of the SVHN dataset better than ResNet18. However, any gains in classification performance for this algorithm would not outweigh the benefits of increasing step 1 performance. Initial efforts should be focused on improving ROI detection.

## Conclusion

An attempt at accurately detecting and classifying digits in images was given in this project. The two-step algorithm proposed uses MSER with various filtering techniques to identify ROIs that may contain digits. Then, a fine-tuned ResNet18 CNN classifies the digit contained within the regions of interest and further filters out unlikely candidates. The algorithm performs well-detecting ROIs for sharp images that contain limited complicated blobs and bold text.

The CNN performs well classifying digits within the image squares, as indicated by the accuracy on the test dataset. More work should be done to improve the detection of the ROI detector by using state-of-the-art deep learning models to detect text. The results of this project's algorithm would be greatly improved by modernizing the ROI detector.

