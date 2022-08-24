# Support Vector Machines


_If you don't have the library installed, you can refer to: [Getting Started page](../GettingStarted/GettingStarted.md)_

Support Vector Machines (abbreviated as SVM) are supervised learning algorithms that can be used for classification and regression problems. They are based on the idea of finding a hyperplane that best separates the features into two classes.

We will use data from the [Wisconsin breast cancer diagnostic dataset](https://www.kaggle.com/code/buddhiniw/breast-cancer-prediction) to train the machine learning to be able to predict if someone has or not diabetes based in several parameters.

The Wisconsin dataset contains 32 parameters of a breast cancer, like its radius mean, its area mean, its smoothness mean, etc and as an output the dignosis of the cancer wether it's begnine ou maligne.

First, we will load the dataset. Then, normalizing the data, to split between test and training datasets. After that, we will train the SVM model and finally measuring its performance by predicting on test data.