---
layout: page
title: Hateful Memes Challenge - Next Move
description: CS7643 Deep Learning final project, attempts improvement of multimodal classification for hateful memes
img: assets/img/publication_preview/mean_meme.png
importance: 1
category: academic
---

<a href="{{ 'assets/pdf/cs7643_final_project.pdf' | relative_url}}" target="_blank" rel="noopener noreferrer" class="float-right">Report <i class="fas fa-file-pdf" style="font-size: 20pt;"></i></a>

## Introduction - Background - Motivation

This project focuses on improving hate speech classification in multimodal memes. The aim is to enhance the Visual BERT COCO model and achieve accuracy comparable to or better than human performance. To accomplish this, the project employs semi-supervised learning techniques to re-label data from the Memotion Dataset, thereby expanding the training dataset and improving model performance.

Multimodal reasoning plays a crucial role in understanding real-world problems that involve interactions between humans and their environment. The project addresses the challenge of confounding memes, where the text and image may contradict each other, by refining the Visual BERT COCO model. By tackling this limitation, the project aims to achieve more accurate hate speech classification in multimodal memes.

The project emphasizes the significance of proper content control, particularly with regard to hate speech on social media platforms. By developing a model that can accurately and autonomously identify and remove hateful memes, the project seeks to reduce the spread and impact of hate speech online. The Hateful Memes dataset, provided by Facebook AI, serves as the foundation for training hate speech classification models, showcasing its importance in advancing multimodal reasoning and multimodal model development.

By leveraging semi-supervised learning and utilizing the Memotion Dataset, the project expands the training dataset to improve the performance of the Visual BERT COCO model. With the aim of achieving accuracy comparable to human judgment, this project contributes to the ongoing efforts to mitigate hate speech and promote responsible content control in the digital sphere.

## Approach

Our approach is aimed at improving upon the success of the winners of the Hateful Memes Challenge. To achieve this, we focused on expanding the dataset using semi-supervised learning techniques, specifically by adopting a similar architecture to the FixMatch algorithm introduced by Sohn et al. (Figure 1).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/hateful memes/fig_2.jpg" title="Figure 1" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 1. FixMatch algorithm
</div>

We drew inspiration from the implementation and fine-tuning of the Visual BERT model by Velioglu and Rose. Recognizing the limited size of the Hateful Memes dataset (10,000 images), we sought to incorporate additional training data from larger datasets such as VisualGenome, COCO, and Conceptual Captions. We combined the Hateful Memes dataset with the Memotion 7K dataset, resulting in a total of 19,132 memes.

The key aspect of our improved model's performance lies in the extraction of features from the images using a "ResNeXT-152 based Mask-RCNN model trained on VisualGenome." These visual embeddings are then projected into the textual embedding space before passing them through the transformer layers of the Visual BERT model (Figure 2).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/hateful memes/fig_3.jpg" title="Figure 2" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 2. Visual BERT process
</div>

We prepared the environment and model setup following the guidelines provided by Team HateDetectron's successful implementation. This involved installing Facebook AI's Multimodal Framework (MMF) package, loading the Hateful Memes dataset into the framework, and integrating the Memotion 7K dataset.

The initial training metadata consisted of 8,928 reliably labeled memes, including data from the Hateful Memes training dataset, the hateful meme Dev seen dataset, and a subset of labeled memes from the Memotion 7K dataset. The Visual BERT model, pretrained on COCO, was trained using these features and metadata.

For semi-supervised learning, we created new metadata for the remaining unlabeled memes from the Memotion 7K dataset. Pseudo labels were assigned to these memes, and a threshold was set to filter positive and negative pseudo labels. This resulted in 1,534 pseudo-labeled memes.

To enhance the training data, we applied image transformations, such as cutout (Figure 3), to the 1,534 pseudo-labeled memes, generating strongly augmented memes. These augmented memes were then concatenated with the original training metadata, resulting in a new training dataset of 10,462 memes.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/hateful memes/fig_4.jpg" title="Figure 3" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 3. Example of an image with a cutout augmentation
</div>

The Visual BERT model was retrained using the new training metadata and the same hyperparameters. The performance of the model was evaluated on an unseen testing dataset, measuring accuracy and AUROC.

Throughout the process, we iteratively refined the inclusion of pseudo-labeled memes based on analysis and domain knowledge. However, we discovered that incorporating model-selected pseudo-labeled memes did not improve the performance metrics as expected.

We realized that using semi-supervised learning without human review and modification was infeasible. Therefore, we manually reviewed 822 pseudo-labeled hateful memes and identified only 282 as potentially valid. These 282 cherry-picked pseudo-labeled memes were incorporated into the training metadata, but even after retraining the model, the performance metrics did not show significant improvement.

## Experiments and Results

Our objective was to enhance the performance metrics of the baseline model, Visual Bert COCO, with the goal of achieving human-like accuracy (84.7%) in classifying hateful memes. We used the Memotion Dataset 7K and employed semi-supervised learning to enrich the original Facebook AI training data. 

We undertook three stages of incremental training and data analysis. The main performance metrics used were accuracy and AUROC. The baseline model was fine-tuned using the original Facebook AI data and two sets of hyperparameters. The loss function was set as cross-entropy with the optimization algorithm as Adam. The visual embedding dimension was set as 2048.

Two optimal sets of hyperparameters were found via grid search. Following that, three stages of training data enrichment were undertaken. We observed that despite the increase and manual modification of pseudo labels in the training metadata, the model's performance did not significantly improve.

We identified a gap in the data assumption between testing and pseudo-labeled training metadata. The self-attention layer, a unique feature of Visual BERT, played a critical role in this process.

| **Model Hyperparameters** | **1** | **2** |
| --- | --- | --- |
| Steps | 3000 | 3500 |
| Learning Rate | 0.3 | 0.6 |
| Learning Rate Decay | warmup linear | warmup cosine |
| Warm Up | TRUE | TRUE |
| Warm Up Steps | 2000 | 500 |
| Batch Size | 32 | 80 |
| Optimizer Learning Rate | 5e-5 | 5e-5 |

**Table 1:** Best model hyperparameters

| **Dataset** | **Model** | **Validation Acc.** | **Validation AUROC** | **Test Acc.** | **Test AUROC** |
| --- | --- | --- | --- | --- | --- |
| Hateful Memes (Baseline) | 1 | 68.33 | 73.59 | 70.30 | 75.23 |
| | 2 | 70.37 | 72.91 | 72.60 | 76.84 |
| HM + Memotion 328 | 1 | 71.30 | 74.07 | 72.35 | 76.87 |
| | 2 | 74.26 | 74.57 | 73.10 | 78.45 |
| HM + Memotion 328 + Pseudo-labeled 1,534 | 1 | 69.07 | 75.00 | 69.80 | 75.88 |
| | 2 | 70.00 | 75.28 | 72.30 | 77.83 |
| HM + Memotion 328 + Filtered Pseudo-labeled 282 | 1 | 70.56 | 75.14 | 71.90 | 76.55 |
| | 2 | 68.33 | 73.50 | 69.85 | 74.28 |

**Table 2:** Model performance

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/hateful memes/fig_6.jpg" title="Figure 34" class="img-fluid rounded z-depth-1" width=600 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 4. Training and validation losses
</div>

## Future Work
In the future, potential avenues of research could include introducing new classification layers, pretraining on additional datasets like COCO for improved transfer learning, using better text encoders to replace the BERT text encoder, and using external sources for additional image content information. Altering model architecture may enhance performance but requires detailed knowledge of current models.

## Conclusion
Our exploration sought to improve performance in the Hateful Memes Challenge by using a semi-supervised learning technique to introduce more data into the training set. Despite various approaches, we found that semi-supervised learning for hateful meme detection is challenging without human interaction and filtering. We also found that adding different structured data to the training dataset did not substantially impact model performance. It is our hope that these insights will inform future work to help reduce hate speech prevalence online.