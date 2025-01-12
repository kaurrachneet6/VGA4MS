# GVS: Gait Video Study 
## A Vision-Based Framework for Predicting Multiple Sclerosis and Parkinson's Disease Gait Dysfunctions - A Deep Learning Approach
This is the code for the paper R. Kaur, R. Motl, R. Sowers, and M. E. Hernandez "A Vision-Based Framework for Predicting Multiple Sclerosis and Parkinson's Disease Gait Dysfunctions - A Deep Learning Approach," in IEEE Journal of Biomedical and Health Informatics, doi: 10.1109/JBHI.2022.3208077., https://ieeexplore.ieee.org/document/9896159

### Authors:
* Rachneet Kaur, Department of Industrial and Enterprise Systems Engineering at the University of Illinois at Urbana-Champaign, rk4@illinois.edu, https://kaurrachneet6.github.io/
* Robert Motl, Department of Physical Therapy at the University of Alabama at Birmingham, Birmingham, AL 35233, USA
* Manuel Hernandez, Department of Kinesiology and Community Health at the University of Illinois at Urbana-Champaign, mhernand@illinois.edu, http://kch.illinois.edu/hernandez
* Richard Sowers, Department of Industrial and Enterprise Systems Engineering and the Department of Mathematics at the University of Illinois at Urbana-Champaign, r-sowers@illinois.edu, http://publish.illinois.edu/r-sowers/

### Abstract
Multiple sclerosis (MS) and Parkinson's disease (PD) are chronic neurodegenerative disorders associated with high fall risk in older adults. While gait evaluation is common, few studies have focused on the differentiation of neurological disorders using gait analysis. This study examined the effectiveness of a novel **v**ision-based **g**ait **a**nalysis framework **for** **MS** prediction using gait video data collected during self-paced walking from MS, PD patients and age, weight, height and gender-matched healthy older adults. We extracted characteristic 3D joint keypoints from multi-view digital camera videos, and proposed a video data driven methodology to classify strides of persons with MS (PwMS) from healthy controls and PD subjects that may generalize across different walking tasks, subjects and both together. We presented a comprehensive quantitative comparison of 16 diverse traditional machine and deep learning (DL) algorithms.
When generalizing from comfortable walking (W) to walking while talking (WT), multi-scale residual neural network achieved the optimal perfect subject classification accuracy and AUC; for subject generalization in W trials, residual neural network resulted in the best accuracy and AUC of 78.1% and 0.87, respectively, and 1D convolutional neural network (CNN) had best accuracy and AUC of 75% and 0.77, respectively, in WT trials. Finally, when generalizing over new subjects in different tasks, again 1D CNN had the top subject classification accuracy and AUC of 79.3% and 0.93, respectively. This work is the first attempt to apply and demonstrate the potential of DL with a multi-view digital camera-based gait analysis framework for MS prediction. 
These results have future implications to design a cost-effective system to be deployed in hospitals, homes or workplaces to automatically diagnose neurological gait in older adults for a more efficient treatment. 

### Dependencies:
* Python 3.6
* The versions of Python packages can be found in the file X.txt/X.yml

### Code structure:
#### Data Preperation 
* **Downsample.ipynb**: To convert videos to frames at a 30FPS (frames per second) and downsample if needed. 
* **OpenPose2Dkeypoints.ipynb**: To compute 2D coordinates from extracted frames using OpenPose package https://github.com/CMU-Perceptual-Computing-Lab/openpose.
* **CameraCalibartion.ipynb**: To compute the intrinsic and extrinsic camera matrices for converting the 2D pixel units to centimeters (cm) in 3D world coordinates. We consider the origin (x=0, y=0, z=0 set at the start of the treadmill) of the treadmill as the origin of the world coordinates.
* **PostProcessing2Ddata.ipynb**: To manage left-right swap issue and missing value treatment for lower body frames and retaining the coorect body markers for the feet frames. Also computes the statistics of the postprocessing steps. 
* **2Dto3D_subject_wise_checks.ipynb**: To double check the extrinsic camera parameters (computed rotation and translation matrices) for both the front and side camera for each subject. This check is handy when say a couple of subjects had a slightly different orientation of the camera than it is calibrated for. In such cases then, we can adjust the parameters for these subjects to avoid incorrect 3D coordinates. 
* **Convert2Dto3D.ipynb**: To use the computed intrinsic and extrinsic camera matrices (via the CameraCalibration.ipynb) for both the front and side camera to convert all the extracted 2D pose coordinates to 3D real-world pose coordinates http://www.cs.cmu.edu/~16385/s17/Slides/11.1_Camera_matrix.pdf.
* **Postprocessing3Ddata.ipynb**: Postprocessing the created 3D keypoints (via intrinsic and extrinsic matrix) for lower body and feet and combining together both front and side view 3D coordinates to get the final combined coordinates. Further, scaling all average hip heights to a constant to normalize for subject heights in our dataset.
* **IdentifyHSR.ipynb**: Identifying frames with HSRs in each video for each cohort and trial to establish break points and also evaluate the corresponding HSR labelling via the ground truth available. Further, downsampling with smoothing to define fixed shape of the input tensor for models. 
* **StrideStats.ipynb**: Calculating the stats for strides in each framework (HOA-W/WT, MS-W/WT, PD-W/WT). This will help write stats for count of strides used in training and testing set of each framework, 1. task generalization a) W to WT, and 2. subject generalization a) W, b) WT.

#### CoP validation
To validate the estimates 3D poses via CoP computed on the treadmill 
* **CoPvalidation_make_viz_dataframes.ipynb**: This code makes (and saves as csv) the dataframe for each video containing their relevant frames, corresponding feet coordinates for these frames and their relative treadmill extracted CoP values 
* **CoPvalidation_evaluate_visually_quantitatively.ipynb**: This code qualitatively and quantitatively validates the center of mass trajectory of estimates body coordinates with their respective treadmill extracted CoP's on a frame-wise/support group-wise basis. This code creates the csv files to further do the final qualitative and quantitative analysis in CoPvalidation_result_analysis.ipynb.
* **CoPvalidation_result_analysis.ipynb**: This code will do the final qualitative and quantitative validation for CoP and centroid of BoS. We compile the quantitative results of distances and correlations between CoPs and centroids for each cohort, trial (W/WT) and support type (Single/Double). Further, we will plot the butterfly diagrams for CoPs and centroids to qualitatively vizualize how treadmill CoPs match with BoS's centroid values. 

#### Utility functions 
* **imports.py**: package imports
* **split.py**: Function definition for StratifiedGroupKFold for handling imbalance in case of Group K fold https://github.com/scikit-learn/scikit-learn/issues/13621
* **task_gen_traditionalML.py**: Functions to read extracted features data, tune and evaluate traditional ML models, plot confusion matrices and ROC curves for tuned models in task generalization frameworks.
* **subject_gen_traditionalML.py**: Functions to read extracted features data, tune and evaluate traditional ML models, plot confusion matrices and ROC curves for tuned models in subject generalization frameworks.
* **cross_gen_traditionalML.py**: Functions to read extracted features data, tune and evaluate traditional ML models, plot confusion matrices and ROC curves for tuned models in cross (task + subject) generalization frameworks.
* **gait_data_loader.py**: Defines the Data loader for the deep learning frameworks 
* **DLutils.py**: Contains definition of general utilities like setting random seed for replicability etc. used across all three generalization frameworks and deep learning models 
* **task_gen_DLtrainer.py**: Utility functions like train, resume train, evaluate etc. for training the deep learning models on the task generalization framework
* **subject_gen_DLtrainer.py**: Utility functions for training the deep learning models on the subject generalization frameworks
* **cross_gen_DLtrainer.py**: Utility functions for training the deep learning models on the cross generalization frameworks
* **cnn1d_model.py**: CNN1D model for time series classification with and without positional encoding
* **positional_encoding.py**: Positional encoding https://pytorch.org/tutorials/beginner/transformer_tutorial.html 
* **RESNET_model.py**: Residual 1D model for time series classification with and without positional encoding
* **padding.py**: Implementation for "padding = same" in Pytorch https://github.com/rwightman/pytorch-image-models/blob/master/timm/models/layers/padding.py#L28
* **MULTISCALE_RESNET_model.py**: Multiscale Residual network for time series classification https://github.com/geekfeiw/Multi-Scale-1D-ResNet
* **TCN_model.py**: Temporal Convolutional Model 
* **RNN_model.py**: Vanilla Recurrent Neural Network (Uni- and Bi-directional versions)
* **GRU_model.py**: Gated Recurrent Unit model (Uni- and Bi-directional)
* **LSTM_model.py**: Long-short term memory model (Uni- and Bi-directional)


#### Machine Learning 
* **SummaryStats.ipynb**: Creating the summary statistics file for the traditional ML algorithms on task/subject generalization frameworks. We use the summary statistics as range, CoV and asymmetry between the right and left limbs as the features to input to the traditional models requiring fixed size 1D input for each training/testing set sample.
* **TaskGeneralize_MLtraditional.ipynb**: Traditional ML algorithms on task generalization framework 1: train on walking (W) and test on walking while talking (WT) and 2: train on virtual beam walking (VBW) and test on virtual beam walking while talking (VBWT) to classify HOA/MS/PD strides and subjects. We use majority voting for subject classification. We retain only subjects common to both train and test sets for this analysis. 
* **SubjectGeneralize_MLtraditional.ipynb**: Traditional ML algorithms on subject generalization frameworks, 1: W, 2: WT, 3: VBW, 4: VBWT using cross validation (we use stratified group K folds here) to classify HOA/MS/PD strides and subjects. We use majority voting for subject classification. Further, to compare across the four sub-frameworks of subject generalization, we retain only common subjects across the four frameworks and then rank the frameworks on the basis of best to worst subject generalization performance/capability.
* **Task&SubjectGeneralize_MLtraditional.ipynb**: Traditional ML algorithms on task+subject generalization frameworks, 1. train on some subjects in W and test on separate set of subjects in WT, 2. train on some subjects in VBW and test on separate set of subjects in VBWT, to classify HOA/MS/PD strides and subjects. We use majority voting for subject classification. We use cross validation here but further retain only subjects present for W in training for 1. and only subjects present for WT in testing for 1., and similarly for 2.
* **TaskGeneralize.py**: A runner file for all deep learning algorithms on task generalization framework 1: train on walking (W) and test on walking while talking (WT) and 2: train on virtual beam walking (VBW) and test on virtual beam walking while talking (VBWT) to classify HOA/MS/PD strides and subjects. 
* **SubjectGeneralize.py**: A runner file for all deep learning algorithms on subject generalization frameworks, 1: W, 2: WT, 3: VBW, 4: VBWT using cross validation (we use stratified group K folds here) to classify HOA/MS/PD strides and subjects.
* **CrossGeneralize.py**: A runner file for all deep learning algorithms on task-subject cross generalization framework i.e. train on some subjects of trial W and test on rest of the subjects in trial WT, using cross validation (we use stratified group K folds here) to classify HOA/MS/PD strides and subjects.
* **config_files/** contains configuration templates to optimize hyperparamaters for the deep learning models for main classification results as well as ablation results


#### Discussion analysis
* **Ablation_TaskGen_MLTraditional.ipynb**: Ablation Study on Task generalization framework W -> WT with Traditional ML models only. 
    * We will explore the performance of using feet coordinates only (merged from both front and side camera views), feet + ankle coordinates (merged from both front and side camera views), feet + ankle (merged from both front and side camera views) + knee (from the single front camera view) coordinates and all (feet + ankle (merged from both front and side camera views) + knee + hip (from the single front camera view)) coordinates on the traditional ML models. We will add frame count as an extra feature to all these data streams, i.e. we will explore, 1) feet + frame count; 2) feet + ankle + frame count; 3) feet + ankle + knee + frame count; 4) All (Default models).
    * In our main traditional models, total 91 features (including the frame count and 90 CoV, range and asymmetry of the body coordinate features) were used.
* **Ablation_SubjectGen_MLTraditional.ipynb**: Ablation Study on Subject generalization frameworks 1) W, 2) WT with Traditional ML models only. 
* **Ablation_CrossGen_MLTraditional.ipynb**: Ablation Study on Task-subject Cross generalization framework with Traditional ML models only.
* **Ablation Study for the DL models** is done using changes done to the utility functions task/subject/cross_gen_DLtrainer.py respectively.
* **TaskGen_PermutationImportance.py**:  Permutation Importance based feature importance for the task generalization framework. Basically, for each feature of interest, say, right knee, we randomly shuffle the right knee-based features in our data, i.e. right knee-x/y/z 5 times and each time compute the evaluation metrics by predicting on this new shuffled test set data. The trained model used for predictions is the best tuned model for the task generalization framework. It leverages functions from the gait_data_loader.py and task_gen_DLtrainer.py utility functions.  
* **SubjectGen_PermutationImportance.py**: Permutation Importance based feature importance for the subject generalization frameworks (W and WT). Basically, for each feature of interest, say, right knee, we randomly shuffle the right knee-based features in our data, i.e. right knee-x/y/z 5 times and each time compute the evaluation metrics by predicting on this new shuffled test set data for each of the 5 test folds in the 5-fold CV. The training folds remain as is and the best tuned subject gen model (for W and WT separately) is used for making predictions on the shuffled test folds. It leverages functions from the subject_gen_DLtrainer.py utility function.
* **CrossGen_PermutationImportance.py**: Permutation Importance based feature importance for the cross generalization framework. Basically, for each feature of interest, say, right knee, we randomly shuffle the right knee-based features in our data, i.e. right knee-x/y/z 5 times and each time compute the evaluation metrics by predicting on this new shuffled test set data for each of the 5 test folds in the 5-fold CV. The training folds remain as is and the best tuned cross gen model is used for making predictions on the shuffled test folds. It leverages functions from the cross_gen_DLtrainer.py utility function.
* **PermutationImportanceVizualizations.ipynb**: Vizualizing the permutation feature importance results for best task, subject and cross generalization models 
* **TaskGen_SHAP.ipynb**: SHAP feature importance (https://shap.readthedocs.io/en/latest/) for Task generalization framework 
* **SubjectGenW_SHAP.ipynb/SubjectGenWT_SHAP.ipynb**: SHAP feature importance (https://shap.readthedocs.io/en/latest/) for Subject generalization W and WT frameworks respectively
* **CrossGen_SHAP.ipynb**: SHAP feature importance for Cross generalization framework
* **SeverityAnalysis.ipynb**: We check the correlation (using visualization) between the classification performance (using binary correctly/incorrectly classified subject and continuous no. of strides correctly classified for each subject) and SPBB score (range 0-12) representing the frailty of the individual.

### Citation:
If you use this code, please consider citing our work:

(1) https://ieeexplore.ieee.org/document/9896159
```
@ARTICLE{9896159,
  author={Kaur, Rachneet and Motl, Robert W. and Sowers, Richard and Hernandez, Manuel E.},
  journal={IEEE Journal of Biomedical and Health Informatics}, 
  title={A Vision-Based Framework for Predicting Multiple Sclerosis and Parkinson's Disease Gait Dysfunctions - A Deep Learning Approach}, 
  year={2022},
  volume={},
  number={},
  pages={1-12},
  doi={10.1109/JBHI.2022.3208077}}
```
(2) https://ieeexplore.ieee.org/document/9311191
```
@ARTICLE{9311191,  
author={R. {Kaur} and Z. {Chen} and R. {Motl} and M. E. {Hernandez} and R. {Sowers}},  
journal={IEEE Transactions on Biomedical Engineering},   
title={Predicting Multiple Sclerosis from Gait Dynamics Using an Instrumented Treadmill – A Machine Learning Approach},   
year={2020},  
volume={},  
number={},  
pages={1-1},  
doi={10.1109/TBME.2020.3048142}}
```

(3) https://ieeexplore.ieee.org/abstract/document/8857604
```
@inproceedings{kaur2019exploring,
  title={Exploring Characteristic Features in Gait Patterns for Predicting Multiple Sclerosis},
  author={Kaur, Rachneet and Menon, Sanjana and Zhang, Xiaomiao and Sowers, Richard and Hernandez, Manuel E},
  booktitle={2019 41st Annual International Conference of the IEEE Engineering in Medicine and Biology Society (EMBC)},
  pages={4217--4220},
  year={2019},
  organization={IEEE}
}
```
