# Machine Learning for Assessing Damage in Buildings Using UAV Data

## Overview
The thesis is devoted to solving the urgent applied task of automated detection and quantitative counting of damaged windows using Unmanned Aerial Vehicle (UAV) data for rapid assessment of infrastructure damage scales. <br>
During the research, the specifics of aerial imagery were analyzed, and the inefficiency of existing open datasets for this task was established, leading to the necessity of creating, cleaning, and balancing a custom specialized dataset. A comparative analysis of convolutional neural network architectures was conducted. It was established that YOLO models demonstrate the highest robustness to complex facade backgrounds. <br>
The impact of model scaling, augmentation modes, and inference optimization methods was investigated. It was shown that the use of excessive geometric augmentations destroys the spatial features of small objects. The optimal balance between precision, recall, and computational speed was found to be provided by the YOLO11s configuration combined with Test-Time Augmentation. <br>
A mathematical post-processing algorithm for inference results was developed and implemented, including multi-criteria filtering (by confidence score, object area) and Non-Maximum Suppression (NMS). The developed system is ready for integration into monitoring software complexes to accelerate the damage recovery process.  <br>

## Objectives
- analyze the current state of research and existing computer vision methods in the field of infrastructure damage detection using UAV data;
- investigate and select optimal architectures of convolutional neural networks for the task of detecting small objects in high-resolution images;
- generate, clean and balance a dataset of aerial photographs with damaged window markings for model training and validation;
- experiment with various approaches to improving model accuracy (data augmentation, confidence threshold adjustment, test-time augmentation, etc.);
- assess the quality of the developed solution using standard metrics (Precision, Recall, mAP, F1-score) and analyze the error in counting the number of objects;

## Dataset
A custom dataset was prepared based on UAV images containing damaged building windows.<br>
Data preparation included: Data cleaning, Image annotation, Class balancing, Dividing the dataset into training and validation subsets, Quality control of annotations. <br>
Three versions of the dataset were generated in the work. The third version became the basis for all further research. <br>
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mAP@0.5</th>
      <th>mAP@0.5:0.95</th>
      <th>Precision</th>
      <th>Recall</th>
      <th>F1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Model_V1</th>
      <td>0.368614</td>
      <td>0.169824</td>
      <td>0.382910</td>
      <td>0.533496</td>
      <td>0.445830</td>
    </tr>
    <tr>
      <th>Model_V2</th>
      <td>0.349119</td>
      <td>0.154695</td>
      <td>0.361379</td>
      <td>0.478632</td>
      <td>0.411822</td>
    </tr>
    <tr>
      <th>Model_V3</th>
      <td>0.493211</td>
      <td>0.211139</td>
      <td>0.507229</td>
      <td>0.567106</td>
      <td>0.535498</td>
    </tr>
  </tbody>
</table>
</div>
The quality and balance of the dataset were analyzed to determine their impact on model performance and it was found that data quality critically affects model performance. <br>

## Main
### Object Detection Models
Several CNN-based object detection approaches were investigated: Faster R-CNN, SSD, YOLO
<Figure size 640x480 with 1 Axes><img width="562" height="455" alt="image" src="https://github.com/user-attachments/assets/44e8d441-9ebb-4e8c-b195-4be9c74cbc08" />
YOLO provided the best balance between speed, accuracy, and the possibility of further optimization. SSD showed poor detection results, and Faster R-CNN significantly overestimated the number of objects and generated a large number of false positives. <br>
Therefore, the YOLO family of models was chosen for further research. <br>

### Evaluation Metrics
Model performance was evaluated using: Precision, Recall, mAP (Mean Average Precision), F1-score.

### Accuracy assessment
- Augmentation
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>exp</th>
      <th>model</th>
      <th>mAP@50</th>
      <th>mAP@50-95</th>
      <th>Precision</th>
      <th>Recall</th>
      <th>F1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>v3_baseline</td>
      <td>yolo11n.pt</td>
      <td>0.564221</td>
      <td>0.231534</td>
      <td>0.562030</td>
      <td>0.648498</td>
      <td>0.602176</td>
    </tr>
    <tr>
      <th>2</th>
      <td>v3_strong</td>
      <td>yolo11n.pt</td>
      <td>0.540251</td>
      <td>0.214106</td>
      <td>0.539533</td>
      <td>0.664071</td>
      <td>0.595359</td>
    </tr>
    <tr>
      <th>1</th>
      <td>v3_mild</td>
      <td>yolo11n.pt</td>
      <td>0.446125</td>
      <td>0.165090</td>
      <td>0.501267</td>
      <td>0.569522</td>
      <td>0.533219</td>
    </tr>
  </tbody>
</table>
</div>
Light augmentations showed the best results, while strong ones worsened the quality of detection.
- Increasing the size of the input image
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>exp</th>
      <th>model</th>
      <th>mAP@50</th>
      <th>Precision</th>
      <th>Recall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>v3_img1024</td>
      <td>yolo11n.pt</td>
      <td>0.567931</td>
      <td>0.58773</td>
      <td>0.626251</td>
    </tr>
  </tbody>
</table>
</div>
Increasing the input image size to 1024 pixels did not yield a significant increase in quality.
- Patch-based learning
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>model</th>
      <th>mAP@50</th>
      <th>mAP@50-95</th>
      <th>Precision</th>
      <th>Recall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>v3_patch_model</td>
      <td>0.513151</td>
      <td>0.208947</td>
      <td>0.543454</td>
      <td>0.63515</td>
    </tr>
  </tbody>
</table>
</div>
The patch-oriented approach led to a large number of duplicates and errors in finding objects. <br>

Thus, it was found that the greatest impact on the accuracy of the model is the quality and balance of the dataset.

### Optimal architecture
For this task, the YOLOv11s model provides a good balance between speed, stability, and detection quality. <br> 
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>model</th>
      <th>mAP@50</th>
      <th>mAP@50-95</th>
      <th>Precision</th>
      <th>Recall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>v3_y11s</td>
      <td>0.580322</td>
      <td>0.247619</td>
      <td>0.587344</td>
      <td>0.68079</td>
    </tr>
  </tbody>
</table>
</div>
<IPython.core.display.Image object><img width="2250" height="1500" alt="image" src="https://github.com/user-attachments/assets/dee6d71e-4898-4af2-8f5b-15c45bd6f2e5" />
<IPython.core.display.Image object><img width="2250" height="1500" alt="image" src="https://github.com/user-attachments/assets/a231b06f-f097-4280-8d2e-cf04ba787a4c" />

### Methods for improving the quality of final detection.
- Confidence Threshold
<img width="731" height="307" alt="image" src="https://github.com/user-attachments/assets/16e29df8-f229-475c-98b3-630f01dae37d" />
The influence of the model confidence threshold was analyzed, a confidence threshold of 0.2 provides the best balance between Precision and Recall.
- Test-Time Augmentation
<img width="715" height="91" alt="image" src="https://github.com/user-attachments/assets/8c464f7c-a98b-4a7b-91df-81cc43771788" />
Provided a small but consistent improvement in detection quality, especially for complex and small damaged objects.
- Post-processing evaluation
<img width="622" height="123" alt="image" src="https://github.com/user-attachments/assets/f7609f95-ef0d-4477-a49e-68cb87c3d3bf" />
Post-processing using the Non-Maximum Suppression algorithm was implemented. This improved the accuracy of object counting and reduced the mean absolute error by 25%.

### Model results
<Figure size 700x700 with 1 Axes><img width="619" height="624" alt="image" src="https://github.com/user-attachments/assets/2e6f15e9-b39a-46c9-b07d-7cb7dfd0a878" />
<Figure size 1200x400 with 1 Axes><img width="996" height="393" alt="image" src="https://github.com/user-attachments/assets/949022ca-7bb5-42a4-86a6-1a5ab41341be" />
<Figure size 700x400 with 1 Axes><img width="609" height="393" alt="image" src="https://github.com/user-attachments/assets/85731496-520d-45fc-904d-e838a680091f" />
For most images, the error is small, and significant errors occur only in some complex examples, with noise, partial overlap, and low quality of individual objects. <br>
The results obtained show that the model is able not only to detect damaged windows, but also to perform their quantitative counting with acceptable accuracy. <br>
The quality of detection depends on the quality of the input data.

## Inference
The developed method is a combination of YOLO11s + TTA + post-processing. <br>
The results obtained confirm the possibility of using the proposed approach for tasks of operational monitoring and initial assessment of building damage with the possibility of further optimization.


## Technologies
Python
YOLOv8
YOLOv11
PyTorch
OpenCV
NumPy
Pandas
Matplotlib
Jupyter Notebook
