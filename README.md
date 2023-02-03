# Classifying-as-Benign-or-Malignant-with-Decision-Trees
REFERENCES:
*    [Induction of Decision Trees](https://link.springer.com/article/10.1007/BF00116251) - Research Paper 

*    [Scikit Learn](https://scikit-learn.org/stable/modules/tree.html) decision tree documentation
*   [Breast Cancer dataset](https://archive.ics.uci.edu/ml/machine-learning-databases/breast-cancer-wisconsin/) - UCI Machine learning database
*   [Medium article ](https://medium.com/@chyun55555/decision-tree-classifier-with-scikit-learn-from-python-e83f38079fea) for modeling decision trees
*  [Hands-On Machine Learning book](https://www.amazon.com/Hands-Machine-Learning-Scikit-Learn-TensorFlow/dp/1491962291) for decision trees and random forests
* [ Medium article](https://medium.com/@jaimejcheng/data-exploration-and-visualization-with-seaborn-pair-plots-40e6d3450f6d) for Seaborn pair plots

Here are some of the pros and cons of decision trees:
----------------------------------------------------------

Advantages
*   Decision trees are logarithmic in cost, meaning that this is not very intensive, especially when used on high-features datasets in comparison with other models.
*   White box, meaning that we can actually understand how this works
*   Minimal data preparation needed

Disadvantages
*   Prone to overfit, we could prune to see if that helps
*   Slightly unstable, there might be better trees that represent the true population better
*   Requires a balanced dataset without "inadequate" attributes. Meaning we can't have several examples where the same set of attributes are indicative of different classes 

----------------------------------------------------------

This is a dataset with 10 features, that describe features derived from images of breast masses. They describe the characteristics of the cell nuclei
* Each object in the dataset has a value of 2 or 4, 2 for benign and 4 for objects that are malignant.
* The goal is to correctly classify an object based on its attributes.

## Data cleaning Pipeline with Scikit-Learn
<img width="429" alt="image" src="https://user-images.githubusercontent.com/79114425/205465831-27353650-3d34-4ed7-b336-c8731b1cc674.png">

* We create a class using BaseEstimator to include in a scikit learn pipeline that deals with certain values in the data, primarily making certain values numeric, and dealing with the nulls
* The class does not need to have code that gets initialized. Because this is a transformer, we don't need to add any code in the fit function

<img width="249" alt="image" src="https://user-images.githubusercontent.com/79114425/205465861-150f1b95-642e-4daa-b2f0-47b70052a183.png">

* We also add a MinMaxScaler in the pipeline so this can be used for other models we might make as well, and to deal with the diagnosis column (which has values as 2 or 4 currently). The result is that malignant masses will have a value of 1, and 0 for benign masses

## The data

<img width="379" alt="size_pair_plot" src="https://user-images.githubusercontent.com/79114425/203873082-dfdfae80-10d9-4ca1-81f7-d0a6d1f92aaf.png">

> A pair plot that shows the relationships between the size attributes. I thought this was important because from my personal knowledge, size is a common indicator of malignant tumors

<img width="334" alt="Feature_deistribution" src="https://user-images.githubusercontent.com/79114425/203873400-305029e8-3a7c-4934-877a-e70d10aed3ef.png">

> Below is the distribution of (some!) of the features, split by the benign and malignant. As you can see, malignant tumors are generally associated with larger values for the given attributes



Another thing I checked was the "adequacy" of the data. Based on a research paper, "Induction of Decision Trees" written by J.R. Quinlan, it is ideal for decision trees to be used in datasets where the same set of attributes of an object, always results in the same classification
We check this in the code, finding that though there are some duplicates (where attributes are all the same), there are no instance where the attributes are the same BUT the classification is different.

## The model

The model we use is a classification decision tree from scikit-learn which uses the CART algorithm. 
* Meaning that these are binary trees, where each leaf only has up to two children. 
* If we built this with an ID3 algorithm based model, we might see different results

<img width="821" alt="9depth_decision_tree" src="https://user-images.githubusercontent.com/79114425/203873712-9ff66cf5-8e48-47f9-b4c4-226a91514cbe.png">

Running it initially, we find that there is a depth of 9, and an accuracy of 94.16%. We improve on this by customizing some of the default hyper-parameters in scikitlearn.
First we increase the values of min_samples_leaf and min_samples_split, which increases the value of accuracy to 95.62%. Just sets some requirements for when to split and for leaves to be created

Doing this is a form of regularization, that reduces overfitting of the model when running it against the test set. In general, increasing the min_* features and decreasing the max_* features will regularize the model.

Note that these values might change because there is some probability in how trees are constructed, leading to slightly different results.

Finally, we see that the most important factor seems to be the size uniformity.

<img width="327" alt="feature_importance" src="https://user-images.githubusercontent.com/79114425/203880676-ce176bc9-782c-4ed9-985f-f30034dbc08a.png">

## Hyperparameter tuning
We further tune the hyperparameters by optimzing the max_depth. We test depths from 4 to 9, seeing as how there was depth of 9 when no limit was set. This is another form of preventing overfitting.

Because of the differences from each run, we run the models 100 times and take the average to get a more realistic model accuracy from each hyperparameter set


<img width="317" alt="tuning" src="https://user-images.githubusercontent.com/79114425/203874237-ce1276a9-40f4-4bed-8905-b58e81c7e98e.png">

## Conclusion

We find that the optimal max_depth is 6! by a small margin. By regularizing and tuning, we were able to improve the overall model accuracy to nearly 95%

## Random Forest

![image](https://user-images.githubusercontent.com/79114425/216666097-d44bb0a2-e6bb-4b90-9d17-59ddb80439de.png)

Built a random forest from scratch that has a few hyperparameters we can use for adjusting the forest construction. Main ones to note are feature_split and max_samples.
* max_samples affects how many samples are used in the bootstrapping method for each tree in the forest
* feature_split defines how many features each tree will be randomly assigned. We use the squareroot, log, and all features depending on the parameter. 

By implementing a random forest with 128 trees, we actually see an increase of accuracy on the testing data from 95% to nearly 98%!

We also can access items in the cache of the forest, including the original predictions for each tree on each sample.

Thanks for reading:D



