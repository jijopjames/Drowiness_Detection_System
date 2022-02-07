# Drowiness_Detection_System
 A Drowiness Detection System to compairing against differnt model to a benchmark model. This project was made as part of the University Project.


## Dataset
The dataset used for this project is the [NTHU-DDD dataset](http://cv.cs.nthu.edu.tw/php/callforpaper/datasets/DDD/).  

## Files
There are 8 main files and 2 sub-files.

* **1_read_train_test:** Contains code for the video to frame convertion and feature extraction
* **2_annote_train_test:** Contains code for the annotation step NTHU-DDD dataset have a label folder where annotation for each frame is mentioned.
* **3_annote_2:** 2 rows added to start of annotations of evaluation dataset. (drowsiness: -1)
* **4_merge_dfs:** merges all dataframes group into a single merged dataframes.
* **5_final_data:** Combines all merged dataframes into a single final dataset.
* **6_preprocessing:** Its the preprocessing stage. where missing values and other values errors are handled.
* **7_classification:** Different classification models are performed on the dataset.
* **8_clustering:** Different clustering models are performed on the dataset. 

The 2 sub files are:

* **5.1_stats:** contains stats for the dataset
* **6.1_Plotting:** plotting the different graphs

## Project
The Dataset is a video dataset. We need to extract the video into frames so that we can detect the face, we are using the OpenCV library to extracte the frames from the video. We 
make use of Dlib to detect the face. Then using 68 facial landmarks we are extracting the features from this videos into mangable and meaningful numerical data such as:

* **Eye Aspect Ratio (EAR):** It is distance between the points around the Eye. EAR value will be large and relatively constant when the eye is open.
EAR value will almost be zero when the eye blinks.
* **Mouth aspect ratio (MAR):** This Formula resembles EAR with respect to the 68 facial landmark coordinates. It uses the coordinates from 61 to 68 and calculates 
the EAR value. Therefore, it can be used to detect yawning behaviour. Like EAR, the MAR value will be large and relatively constant when the mouth is open. 
MAR value will almost be zero when the mouth is closed.
* **Eye Circularity (EC):** It is a measure that is very closely related to the EAR, but it puts more emphasis on the pupil area. For example, someone with a half 
closed eye or almost closed eye will have a lower eye circularity value when compared to someone with fully opened eyes.
* **Mouth over Eye (MOE):** MOE is a simple ratio between MAR and EAR.
* **PERCLOS:** PERCLOS is the percentage of eyelid closure over the pupil over time and reflects slow eyelid closures rather than blinks. This metric indicates the frequency 
of the closed eye up until this moment. We are using it in a small time window of 90 seconds.
* **Level of Eyebrows (LEB):** It is the average distance between two points from the corner of the eyebrow to the corner of the eye.
* **Size of Pupil (SOP):** This metric is not directly related to the drowsiness of the subject, but fluctuation in pupil size show the fatigue level of the subject.

### Preprocessing
---
We handle the missing values by dropping them. We notice there is abnormal 0’s in eye feature. This occurs when the subject turns and eye is not detected hence all 
feature values becomes 0. To detect this scenario:

* RIGHT_EAR == 0 and LEFT_EAR != 0 is searched in data frame. LEFT_EAR value is selected and copied in to a newly defined EAR column. 
Similarly, RIGHT_EAR is checked and updated in EAR column. This is process is repeated for all the eye feature

Then we perform EDA. Which can be found in File [6.1_Plotting](https://github.com/jijopjames/Drowiness_Detection_System/blob/master/6.1_Plotting.ipynb).

### Results

#### Classification
We have performing 5 classification model with 4 different feature selections method. Then we compare them by using five different evaluation metrics 
(Accuracy, Precision, Recall, F1, ROC ). According to the precision metrics, there are no significant differences between them. All other metrics show a difference between them.
Therefore, according to the results, we conclude that CART-entropy with full features is the best classification model. 

| Classification                  | Accuracy (Avg)| Precision (Avg)| Recall (Avg)| AUC (Avg)| F1 (Avg)|
| ------------------------------- | -------- | --------- | ------ | ------ | ------ |
| KNN-5 with full features        | 0.7101   | 0.7387    | 0.7608 | 0.7496 | 0.7019 |
| KNN-5 with ANOVA                | 0.6900   | 0.7209    | 0.7447 | 0.7326 | 0.6811 |
| KNN-5 with MI                   | 0.6967   | 0.7276    | 0.7484 | 0.7378 | 0.6883 |
| KNN-5 with RFE                  | 0.7037   | 0.7339    | 0.7537 | 0.7437 | 0.6956 |
| CART-gini with full features    | 0.7183   | 0.7538    | 0.7514 | 0.7526 | 0.7129 |
| CART-gini with ANOVA            | 0.7096   | 0.7463    | 0.7434 | 0.7449 | 0.7040 |
| CART-gini with MI               | 0.7091   | 0.7457    | 0.7435 | 0.7446 | 0.7035 |
| CART-gini with RFE              | 0.7197   | 0.7547    | 0.7547 | 0.7539 | 0.7142 |
| NB with full features           | 0.5836   | 0.5898    | 0.8854 | 0.7080 | 0.5342 |
| NB with ANOVA                   | 0.5816   | 0.5854    | 0.9134 | 0.7135 | 0.5274 |
| NB with MI                      | 0.5935   | 0.6041    | 0.8330 | 0.7003 | 0.5543 |
| NB with RFE                     | 0.5849   | 0.5882    | 0.9072 | 0.7137 | 0.5323 |
| KNN-25 with full features       | 0.7141   | 0.7369    | 0.7369 | 0.7557 | 0.7041 |
| KNN-25 with ANOVA               | 0.7004   | 0.7244    | 0.7660 | 0.7446 | 0.6896 |
| KNN-25 with MI                  | 0.7069   | 0.7311    | 0.7688 | 0.7495 | 0.6968 |
| KNN-25 with RFE                 | 0.7085   | 0.7330    | 0.7690 | 0.7506 | 0.6987 |
| CART-entropy with full features | 0.7213   | 0.7560    | 0.7550 | 0.7555 | 0.7158 |
| CART-entropy with ANOVA         | 0.7112   | 0.7474    | 0.7455 | 0.7464 | 0.7056 |
| CART-entropy with MI            | 0.7103   | 0.7463    | 0.7452 | 0.7458 | 0.7046 |
| CART-entropy with RFE           | 0.7211   | 0.7558    | 0.7547 | 0.7553 | 0.7156 |

#### Clustering
We have performing 3 Clustering model. Then we compare them by using different evaluation metrics 
(Estimated number of Cluster, Number of instances, Estimated Noise, Standard deviation of Clusters, Homogeneity, Completeness, 
V-measure, Rand Index, Mutual Information, Fowlkes-Mallows Score, Silhouette Coefficient).



| Evaluation Metrics          | K-MEANS                                           | AGNES                                            | DBSCAN                                           |
| --------------------------- | ------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------ |
| No. of Clusters             | 4                                                 | 3                                                | 1                                                |
| Instances                   | {0: 371, 1: 6646, 2: 936, 3: 2047, 'avg': 2500.0} | {0: 9865, 1: 1, 2: 134, 'avg': 3333.33}          | {-1: 3, 0: 9997, 'avg': 5000.0}                  |
| noise points                | 0                                                 | 0                                                | 3                                                |
| Cluster 0                   | feature1: 19.75, feature2: 36.08, feature3: 10.16 | feature1: 22.00, feature2: 12.95, feature3: 6.17 | feature1: 21.92, feature2: 18.40, feature3: 6.23 |
| Cluster 1                   | feature1: 5.01, feature2: 8.51, feature3: 5.99    | feature1: 0.00, feature2: 0.00, feature3: 0.00   | \-                                               |
| Cluster 2                   | feature1: 14.10, feature2: 11.08, feature3: 6.37  | feature1: 12.48, feature2: 24.88, feature3: 9.42 | \-                                               |
| Cluster 3                   | feature1: 9.18, feature2: 8.41, feature3: 6.06    | \-                                               | \-                                               |
| Homogeneity                 | 0.037                                             | 0.001                                            | 0                                                |
| Completeness                | 0.027                                             | 0.01                                             | 0.062                                            |
| V-measure                   | 0.031                                             | 0.002                                            | 0                                                |
| Adjusted Rand Index         | 0.005                                             | 0.002                                            | 0                                                |
| Adjusted Mutual Information | 0.031                                             | 0.002                                            | 0                                                |
| Fowlkes-Mallows score       | 0.504                                             | 0.705                                            | 0.714                                            |
| Silhouette Coefficient      | 0.478                                             | 0.739                                            | 0.855                                            |
| Inertia                     | 2284138                                           | \-                                               | \-                                               |

