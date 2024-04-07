---
layout: distill
title: Polyp localization and tracking
description: 
img: assets/img/polyps.jpg
importance: 1
category: work
related_publications: true
bibliography: 2024-Projects.bib
tags: CAE

header-includes:
  - \usepackage{algorithm2e}

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.7/katex.min.js"
        integrity="sha512-EKW5YvKU3hpyyOcN6jQnAxO/L8gts+YdYV6Yymtl8pk9YlYFtqJgihORuRoBXK8/cOIlappdU6Ms8KdK6yBCgA=="
        crossorigin="anonymous" referrerpolicy="no-referrer">
</script>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@2.4.1/build/pseudocode.min.css">
<script src="https://cdn.jsdelivr.net/npm/pseudocode@2.4.1/build/pseudocode.min.js">
</script>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.css">
<script src="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.js">
</script>
# Introduction

This project was given as an assignment for an image processing course. The objective was to implement a polyp localization and tracking approach for colorectal cancer screening that was proposed in <d-cite key="8759180"></d-cite>.

Colorectal cancer manifests itself through polyps in the large intestine. It is one of the deadliest cancers. The only way to reduce mortality is through early screening by identifying polyps in a colonoscopy. However, human agents may miss polyps that could otherwise have aided in screening.

The difficulties encountered by Computer-Aided Detection are Intra-class variations (heterogeneity) of polyps (in size, shape, and color) and The fact that often, polyps do not present a strong variation compared to the intestinal mucosa (they are homogeneous with the mucosa).

The article proposes an improvement of a state-of-the-art method reducing jittering.

## Localization

U-net is a convolutional autoencoder, here it takes a frame from the colonoscopy video as an input and outputs a "probability map" indicating whether each pixel belongs to a polyp or not.  after averaging each frame with the previous frame, a threshold of 0.5 is applied to get a binary mask indicating the location of the polyp (segmentation). Finally, we perform an erosion, then we order the components by their size and roundness. If the largest component is the roundest, we keep it and calculate its center.

## Tracking

We used the Lucas–Kanade method to estimate the optical flow to track the polyps between each frame.
If we lose track of it, we train a regression model with its previous locations to predict its location in the current frame. If the predicted location falls inside the image we keep tracking it. Otherwise we run a new localization with u-net. In the original article, They used a binary classifier CNN to test if there were indeed a polyp in the position found by the regression model.

We stop tracking a polyp after tracking it for 10 frames or when it falls out of the image.

## Training 
We used the Kvasir-SEG <d-cite key="jha2020kvasir"></d-cite> dataset to train and test our emplementation.

---
# General specification

Here is a pseudo-code of the algorithm proposed in the aforementioned article.
	
<pre id="polyp" class="pseudocode">
    \begin{algorithm}
    \caption{Polyp localization and tracking}
    \begin{algorithmic}
      \INPUT Colonoscopy Video
	  \STATE $i \leftarrow 0$
	  \STATE previousFrame $\leftarrow$ first frame of the video
	  \STATE listOfPositions $\leftarrow$ []
	  \FOR{frame $\in$ Video}
	  	\IF{$i==0$}	\COMMENT{polyp detection (once every 10 frame)}
	  		\STATE mask1 $\leftarrow$ model(frame)
			\STATE mask2 $\leftarrow$ model(previousFrame)
			\STATE mask $\leftarrow$ average(mask1, mask2)
			\STATE mask.binarize(0.5)
			\STATE mask.errode()
			\STATE contours $\leftarrow$ detect contours in the mask
			\STATE sort the contours according to their size
			\STATE contour $\leftarrow$ biggest in contours
			\STATE center $\leftarrow$ contour.center()
			\STATE listOfPositions.append(center)
		\ENDIF
			\STATE opticalFlow $\leftarrow$ opticalFlow(frame, previousFrame)
			\STATE listOfPositions.append(opticalFlow(listOfPositions[-1])) 
		\IF{listOfPositions[-1] is outside of the image}
			\STATE i $\leftarrow$ 0
		\ENDIF
		\IF{there is jittering}
			\STATE train a regression model from previous positions
			\IF{it is outside of the image}
				\STATE i $\leftarrow$ 0 
			\ENDIF
		\ENDIF
		\STATE previousFrame $\leftarrow$ frame
		\STATE i $\leftarrow (i+1) \mod 10$
	  \ENDFOR
      
      \RETURN listOfPositions
    \end{algorithmic}
    \end{algorithm}
</pre>
<script>
    pseudocode.renderElement(document.getElementById("polyp"));
</script>

---
# Results

The figures show (from left to right) a frame of the colonoscopy, the manually defined binary mask (Ground Truth), and the binary mask generated by U-Net.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/polyps.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
</div>

We applied the system on this video. The blue dot is the polyp's position predicted by the algorithm.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/output.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
    </div>

</div>
