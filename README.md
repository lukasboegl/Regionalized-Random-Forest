# Regionalized Random Forest

Practical implementation of master's thesis in Kartographie und Geoinformation at the University of Vienna, supervised by Dr. Ourania Kounadi with the title:

"Introducing Spatial Heterogeneity via Regionalization Methods in Machine Learning Models for Geographical Prediction: A Spatially Conscious Paradigm."

This can be used to arrive at more performant predictions in a spatial context than a "casual" Random Forest Reegression Model. In certain scenarios, GWR and GW-RF can be outperformed as well.

In this example, the analysis was done for the California Housing Dataset which is available [here](https://www.dcc.fc.up.pt/~ltorgo/Regression/cal_housing).
The dataset was adapted slightly in order to make the Regionalization algorithms able to run. For further reference please do not hesitate to contact me.

Notebooks in this repository:

GWRPRED: Carries out a prediction on the test set using Geographically Weighted Regression

GRF: Carries out a prediction on the test set using Geographically Weighted Random Forest

GW-RF-LISA: Evaluation of spatial performance metrics from GRF.

RF: Carries out a prediction on the test set using Random Forest

RegRF_WARD/AZP/KMEANS/SKATER/MAXP: Carries out a prediction on the test set using Regionalized Random Forest, which is described below:


The abstract of the thesis this repository belongs to is:

Recent years have seen a rapid increase in the capabilities of machine learning models. In this realm, this thesis aims to present a novel approach for predictive modeling which is able to take spatial heterogeneity into account, as well as evaluate various approaches to solving this problem. This is done by introducing regionalization methods in a preprocessing step of the practical workflow. Five different regionalizaton methods are tested: WARD, AZP, KMEANS, SKATER, and MAXP, which are applied to two datasets of varying size, which is another aspect of interest to this research. The dataset in use is the "California Housing Dataset", and the dependent variable is the median house price in an area. The proposed approach is termed RegRF, which can be further divided into three modeling strategies: RegRF_CN, RegRF_SEP, and RegRF_CN-W. The first strategy, RegRF_CN, is a conventional Random Forest with the addition of using the region number as an additional independent variable. RegRF_SEP creates a seperate model for each region, that uses only the data from that region. RegRF_CN-W is a mix of both approaches: It creates a separate model for each region, but with the distinction that it uses all of the training data from the other regions. The region-specifity stems from putting more weight on the training data from the region the model is being trained for. The weight depends on the size of the region, meaning that bigger regions will be weighted more heavily. RegRF is compared to three established predictive modeling methods: Random Forest, Geographically Weighted Regression, and Geographically Weighted Random Forest. The implementation of RegRF has shown its ability to significantly increase the performance of the predictive models in comparison to "non-spatial" Random Forest models (between 5% and 18% increase in R2 values), while only taking a few seconds longer to compute. In some scenarios, RegRF was able to compete with the well established Geographically Weighted Regression (between 1% better and 4% worse judging from the R2 values, depending on the situation), while only requiring a fraction of the computational effort. The third approach, Geographically Weighted Random Forest, was not able to be outperformed when modeling the smaller dataset (between 1% and 7% decrease in R2 values), but in this work this method was not able to finish computation for the larger scale dataset. These results clearly show the potential for the performance of machine learning models to increase when taking spatial phenomena into account. Given the fact that RegRF is not explicitly optimized for the data at hand, further improvements regarding its performance are possible.


The workflow for RegRF is schematically visualized in the figure which is linked ![here](Workflow.pdf). It can be categorized into five steps:

1. Define parameters and carry out the regionalization procedure on the training data polygons. The choice was made to directly use polygons as the creation of the spatial weights matrix would automatically create voronoi polygons when it is run on point data. Additionally, polygons are necessary for joining the test data points in the following step. As the number of regions to look for needs to be specified for every regionalization method except one, finding the best number is the first challenge that needs to be adressed. The result of this is an additional column in the geodataframe which contains the number of the region this observation belongs to.

2. Introduction of the test data (points) to the workflow. To find out which region the observations in the test set are part of, a spatial join is carried out, resulting in the same additional column with the region number, which is of course necessary to be able to create a predictive model and evaluate it.

3. Carry out the first of three prediction approaches, called RegRF_CN (Cluster Number). This is almost identical to a normal RF, the only difference being the use of an additional column - the region number - as an independent variable. After fitting the model, predictions on the test set are carried out and reported.

4. The second prediction approach, which constructs one separate RF for each region, termed RegRF_SEP (Separate), and reporting of prediction performance on the test set.

5. Predict with the final version, called RegRF_W (Weighted). Here, there are multiple RF models, again one for each region, but with an important distinction: For each region, all of the training data is used, but more weight is put on the samples from the region the forest is currently being created for. More precisely, this means that each "region specific" RF will be based on all the other regions as well, but when training the forest, more data will be taken from that specific region than from other regions. The weight is decided from the number of samples in the region, meaning that for bigger regions, the weight will be bigger. When training the forest like this, all the test points will receive a prediction for each "region specific" model. This means that there are multiple predictions for each row, which is a problem for performance evaluation. This is solved by averaging all the predictions.

Depending on the regionalization results and the modeling with the training data, this process is repeated until a satisfactory regionalization result is obtained, after that it is used for predicting the test labels from the test features.
