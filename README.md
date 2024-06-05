# Regionalized Random Forest

Practical implementation of master's thesis in Kartographie und Geoinformation at the University of Vienna, supervised by Dr. Ourania Kounadi with the title:

"Introducing Spatial Heterogeneity via Regionalization Methodsvin Machine Learning Models for Geographical Prediction: A Spatially Conscious Paradigm."


The workflow for RegRF is schematically visualized in the figure below. It can be categorized into five steps:

1. Define parameters and carry out the regionalization procedure on the training data polygons. The choice was made to directly use polygons as the creation of the spatial weights matrix would automatically create voronoi polygons when it is run on point data. Additionally, polygons are necessary for joining the test data points in the following step. As the number of regions to look for needs to be specified for every regionalization method except one, finding the best number is the first challenge that needs to be adressed. As mentioned in section 2.7.6, the plan is to use unsupervised evaluation metrics to assess the result and adapt it if necessary. The result of this is an additional column in the geodataframe which contains the number of the region this observation belongs to.

2. Introduction of the test data (points) to the workflow. To find out which region the observations in the test set are part of, a spatial join is carried out, resulting in the same additional column with the region number, which is of course necessary to be able to create a predictive model and evaluate it.

3. Carry out the first of three prediction approaches, called RegRF_CN (Cluster Number). This is almost identical to a normal RF, the only difference being the use of an additional column - the region number - as an independent variable. After fitting the model, predictions on the test set are carried out and reported.

4. The second prediction approach, which constructs one separate RF for each region, termed RegRF_SEP (Separate), and reporting of prediction performance on the test set.

5. Predict with the final version, called RegRF_W (Weighted). Here, there are multiple RF models, again one for each region, but with an important distinction: For each region, all of the training data is used, but more weight is put on the samples from the region the forest is currently being created for. More precisely, this means that each "region specific" RF will be based on all the other regions as well, but when training the forest, more data will be taken from that specific region than from other regions. The weight is decided from the number of samples in the region, meaning that for bigger regions, the weight will be bigger. When training the forest like this, all the test points will receive a prediction for each "region specific" model. This means that there are multiple predictions for each row, which is a problem for performance evaluation. This is solved by averaging all the predictions.

Depending on the regionalization results and the modeling with the training data, this process is repeated until a satisfactory regionalization result is obtained, after that it is used for predicting the test labels from the test features.

Flussdiagr-1.pdf

![Alt text](/relative/path/to/img.jpg?raw=true "Optional Title")
