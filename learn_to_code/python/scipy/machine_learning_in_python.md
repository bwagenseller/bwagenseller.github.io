# Machine Learning In Python
<!-- clear for public use -->

# Source Material

## PACKT - Python Machine Learning

Some of the material here was obtained from the PACKT book 'Python Machine Learning' by Sebastian Raschka.


# Test Datasets

There are test datasets that can be used; these can be directly brought in with [pandas.read_csv](learn_to_code/python/scipy/pandas?id=loading-from-csv) (just use the web site address in place of the file name).

Listing:
* **Iris Dataset** - https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data
 * May wish to set `header=None` when using [pandas.read_csv](learn_to_code/python/scipy/pandas?id=loading-from-csv).
 
# Updating Weights

Often, when one must update weights, the features and labels are in two separate numpy arrays; each row (features _and_ labels) must be traversed to update the weights. There are a few ways to quickly do this, but one way to do this easily is to use the [zip method](learn_to_code/python/python_basic?id=zipping-lists).

The features or labels cant be changed with this method, but weights can. See the [example of the artificial neuron](learn_to_code/python/scipy/machine_learning_in_python?id=artificial-neuron-example) for an example.

# Normalization

## Standardization

[Standardization](learn_to_code/machine_learning/machine_learning?id=standardization) can be achieved in python with the following.  

As an example, say we have our [features](learn_to_code/machine_learning/machine_learning?id=key-terms-for-the-data) in a numpy array `X`. We can standardize them all at once via:
```
X2_std = (X - X.mean(axis=0)) / X.std(axis=0)
```

`X2_std` will hold the new values.

# Random Row Permutation

> See [here](learn_to_code/python/scipy/numpy?id=random-row-permutation) for how to do this in Python.

Often times, it will be required to randomize the rows in a dataset (for example, the data must be randomized after every [epoch](learn_to_code/machine_learning/ml_key_terms?id=epoch) when using [stochastic gradient descent](learn_to_code/machine_learning/ml_key_terms?id=stochastic-gradient-descent)). Its possible to easily get a randomized list of integers that will correspond to the number of rows in the numpy array.

# Scikit-learn 

The scikit-learn package in Python contains many helpful packages and algorithms that will aid in facilitating machine learning on Python. 

Here are some facts about scikit-learn:
* It is included in the [Anaconda](ubuntu/server_build?id=python-anaconda-install) version of Python (but can be downloaded separately).
* Many algorithms automatically utilize the [One-vs.-Rest (OvR)](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr) approach to the classification of multiple labels.

## Scikit-learn: Datasets

There are many datasets available in Scikit-learn. To import them, first use `from sklearn import datasets`.

The available datasets are:

| `datasets` variable | Description | 
| --- | --- |
| load_boston | Load and return the boston house-prices dataset (regression). |
| load_iris | Load and return the iris dataset (classification). |
| load_diabetes | Load and return the diabetes dataset (regression). |
| load_digits | Load and return the digits dataset (classification). |
| load_linnerud | Load and return the linnerud dataset (multivariate regression). |
| load_wine | Load and return the wine dataset (classification). |
| load_breast_cancer | Load and return the breast cancer wisconsin dataset (classification). |

For example, if we wanted to load the iris dataset, we could run:
```
from sklearn import datasets

if __name__ == "__main__":
	iris = datasets.load_iris()
	X = iris.data
	y = iris.target
	labelNames = iris.get('target_names')
	featuresNames = iris.get('feature_names')
```

The above:
1. Loads the entire dataset to an object called `iris`
2. Loads the [features](learn_to_code/machine_learning/ml_key_terms?id=features) to 'x'.
3. Loads the [labels](learn_to_code/machine_learning/ml_key_terms?id=labels) to 'y'.
 * These are actually coded into integers - `iris.get('target_names')` returns an array whose index match these values and whose value is the original classification.
4. Loads the label names (see \#3 for clarification) to 'labelNames'.
5. Loads the feature names to 'featuresNames' (as the features loaded in \#2 do not have names associated with them; these are those names).

## Scikit-learn: Train/Test Data Split

Scikit-learn has a [train_test_split module](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html) which grants the ability to automatically split the data into a [training data set](learn_to_code/machine_learning/machine_learning?id=training-data) and a [testing data set](learn_to_code/machine_learning/machine_learning?id=testing-data) in basically one line of code:
```
from sklearn import datasets
from sklearn.model_selection import train_test_split

if __name__ == "__main__":
	iris = datasets.load_iris()
	X = iris.data
	y = iris.target
	
	X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)
```
* The above makes an X training set, an X testing set, labels for the training set, and labels for the testing set.
* `test_size=0.3` means the test set should be 30% of the data.
* `shuffle=True` (True is default) means the data will automatically shuffle (meaning the training set and the testing set will be different for each time the data is divided into training and testing data).
* `random_state=0` **IF** `shuffle=True`, this will allow you to set the randomization seed. For production, you will probably want to leave this 'None' (which is the default).


## Scikit-learn: Normalization

Scikit-learn can normalize data in a variety of ways.

!> Please note how [normalization works for data after the model is built](learn_to_code/machine_learning/machine_learning?id=normalizing-data-post-model); typically, the [training data](learn_to_code/machine_learning/machine_learning?id=training-data) is normalized, and then the parameters (mean, standard deviation, row count, etc) are used in the equations to normalize future samples (a.k.a the [testing data set](learn_to_code/machine_learning/machine_learning?id=testing-data) or samples that have no classification)

**<font size="4">Standardization</font>**

Scikit-learn can apply the normalization technique '[standardization](learn_to_code/machine_learning/machine_learning?id=standardization)' in the following manner:
```
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

if __name__ == "__main__":
	
	iris = datasets.load_iris()
	X = iris.data
	y = iris.target
		
	X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)

	#find the parameters necessary for standardization of the training set
	sc = StandardScaler()
	sc.fit(X_train)
	
	print "The mean is {}, the number of samples are {}, and the standard deviation is {}.".format(sc.mean_, sc.n_samples_seen_, sc.scale_)
	
	X_train_std = sc.transform(X_train)
	X_test_std = sc.transform(X_test)
```

In the above example, the object `sc` is created as a `StandardScalar` instance. In order to store the data that will support standardization, we run `sc.fit(X_train)`. Note that 
* We **only** use the training data, we do **not** include the test data
* All necessary parameters to standardize further data are stored in the object `sc`

[As mentioned](learn_to_code/machine_learning/machine_learning?id=normalizing-data-post-model), the parameters used to normalize the data are stored and applied to future data (a.k.a the [testing data set](learn_to_code/machine_learning/machine_learning?id=testing-data) or samples that have no classification). In order to do this, the normalization variables used are stored, and then applied to normalizing any and all new data before it enters the model. In the case of the `sc` object (in accordance with the [standardization](learn_to_code/machine_learning/machine_learning?id=standardization) equation), these variables are the _mean_ and the _standard deviation_. You can access these variables in the `sc` argument:
* **Mean** - `sc.mean_`
* **Standard Deviation** - `sc.scale_`
* **Number of Samples** used to find the mean and standard deviation - `sc.n_sanmples_seen_` (this is not actually used by the standardization equation, but its there for your reference).

In order to standardize other samples / data sets that must be sent through the model, you use the `sc.transform(data)` method (as seen above), which will return all data in standardized format.

This is just to show that the transformed values previously will be the same as if a truncated dataset is sent through (the readings are the same for the truncated data set, which means the values previously determined in sc.fit() are used):
```
	print X_train_std[:5]
	print sc.transform(X_train[:5])
```

Note that if you would like to do this manually without `sc.transform(data)`, you can do it like so. This further proves that the transformed values previously will be the same as if a truncated dataset is used with the raw values used to calculate standardization (namely the mean and standard deviation):
```
	print X_test_std[:5]
	print (X_test[:5] - sc.mean_)/(sc.scale_)
```


## Scikit-learn: One-vs.-Rest

[One-vs.-Rest (OvR)](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr) is a method that allows for multiple classes to be [classified](learn_to_code/machine_learning/machine_learning?id=classification) in a single dataset (most algorithms can only support a binary choice). Scikit-learn has the ability to 'mask' this - you can feed some algorithms a multiclass classification dataset and they will be able to handle them (so long as you convert their classes to integers).

The following scikit-learn machine learning algorithms support OvR natively:
* [Perceptron](/learn_to_code/machine_learning/perceptron) (Scikit-learn implementation [here](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-perceptron))
* [Logistic Regression](/learn_to_code/machine_learning/logistic_regression) (Scikit-learn implementation [here](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-logistic-regression))

# Scikit-learn: Algorithms

Here are some examples of using algorithms from scikit-learn:
* [Perceptrons](/learn_to_code/machine_learning/perceptron), with working Python code [here](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-perceptron).
* [Logistic Regression](/learn_to_code/machine_learning/logistic_regression), with working Python code [here](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-logistic-regression).


## Scikit-learn: Perceptron

Some notes on [Scikit-learn's perceptron](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Perceptron.html#sklearn.linear_model.Perceptron):
* Natively supports [One-vs.-Rest](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr), so long as all classifiers are converted to integers.
* It can be imported with the statement `from sklearn.linear_model import Perceptron`. 
* It's object is created with `ppn = Perceptron(max_iter=400, eta0=0.001, random_state=1)`
 * `max_iter=400` sets the max number of [epochs](learn_to_code/machine_learning/ml_key_terms?id=epoch) (the algorithm may stop if it converges earlier than that).
 * `eta0=0.001` sets the [learning rate](learn_to_code/machine_learning/ml_key_terms?id=learning-rate) (in this case, its .1%)
 * You can set the random seed with `random_state=1`. `random_state` is the seed of the pseudo random number generator to use when shuffling the data (between [epochs](learn_to_code/machine_learning/ml_key_terms?id=epoch) for [stochastic gradient descent](learn_to_code/machine_learning/machine_learning?id=stochastic-gradient-descent)); if set to an int, this is the seed; if None (default) there is no seed.
 * `shuffle = True` (default True) (not listed) Whether or not the training data should be shuffled after each epoch. It is important for [stochastic gradient descent](learn_to_code/machine_learning/machine_learning?id=stochastic-gradient-descent).
* To create the model, use `ppn.fit(X_train_std, y_train)`, where 'X_train_std' contains the features in the normalized training data and 'y_train' contains the labels.
 * Note that this seems to be done in place.
* Predictions can be made with `ppn.predict(X_test_std)`, where 'X_test_std' contains the features in the normalized data (training or testing). This method returns a list of predictions.

Code Example: 
```
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import Perceptron
from sklearn.metrics import accuracy_score

from matplotlib.colors import ListedColormap
import matplotlib.pyplot as plt

import numpy as np

def plot_decision_regions(X, y, classifier, test_idx=None, resolution=0.02):
	# setup marker generator and color map
	markers = ('s', 'x', 'o', '^', 'v')
	colors = ('red', 'blue', 'lightgreen', 'gray', 'cyan')
	cmap = ListedColormap(colors[:len(np.unique(y))])
	# plot the decision surface
	x1_min, x1_max = X[:, 0].min() - 1, X[:, 0].max() + 1
	x2_min, x2_max = X[:, 1].min() - 1, X[:, 1].max() + 1
	xx1, xx2 = np.meshgrid(np.arange(x1_min, x1_max, resolution),
						np.arange(x2_min, x2_max, resolution))
	Z = classifier.predict(np.array([xx1.ravel(), xx2.ravel()]).T)
	Z = Z.reshape(xx1.shape)
	plt.contourf(xx1, xx2, Z, alpha=0.4, cmap=cmap)
	plt.xlim(xx1.min(), xx1.max())
	plt.ylim(xx2.min(), xx2.max())
	# plot all samples
	for idx, cl in enumerate(np.unique(y)):
		plt.scatter(x=X[y == cl, 0], y=X[y == cl, 1], alpha=0.8, c=cmap(idx), marker=markers[idx], label=cl)

	# highlight test samples
	if test_idx:
		X_test, y_test = X[test_idx, :], y[test_idx]
		plt.scatter(X_test[:, 0], X_test[:, 1], c='', alpha=1.0, linewidths=1, marker='o', s=55, label='test set')

if __name__ == "__main__":
	
	#load the data, only using the third and fourth columns for the features 
	iris = datasets.load_iris()
	X = iris.data[:, [2, 3]]
	y = iris.target
	
	print "The corresponding label names are: {}".format(iris.get('target_names'))
	
	#make a training and testing data set
	X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)

	#find the parameters necessary for standardization of the training set
	sc = StandardScaler()
	sc.fit(X_train)
	
	#standardize the training data and the test data using the standardization parameters derived from the training data 	
	X_train_std = sc.transform(X_train)
	X_test_std = sc.transform(X_test)
	
	#create a perceptron object
	ppn = Perceptron(max_iter=400, eta0=0.001, random_state=0)
	
	#create the model
	ppn.fit(X_train_std, y_train)	

	#make predictions on the test data
	y_pred = ppn.predict(X_test_std)
	
	#Show accuracy
	print "Accuracy: {}".format(accuracy_score(y_test, y_pred))
		
	
	X_combined_std = np.vstack((X_train_std, X_test_std))
	y_combined = np.hstack((y_train, y_test))
	
	plot_decision_regions(X=X_combined_std, y=y_combined, classifier=ppn, test_idx=range(105,150))
	plt.xlabel('petal length [standardized]')
	plt.ylabel('petal width [standardized]')
	plt.legend(loc='upper left')
	plt.show()	
```

## Scikit-learn: Logistic Regression

Some notes on [Scikit-learn's logistic regression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html):
* Natively supports [One-vs.-Rest](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr), so long as all classifiers are converted to integers.
* It can be imported with the statement `from sklearn.linear_model import LogisticRegression`. 
* It's object is created with `lr = LogisticRegression(C=1000.0, random_state=0)`
 * `C=1000.0` C is the inverse of regularization strength; it _must_ be a positive float. Smaller values specify stronger regularization.
 * You can set the random seed with `random_state=1`. `random_state` is the seed of the pseudo random number generator to use when shuffling the data (between [epochs](learn_to_code/machine_learning/ml_key_terms?id=epoch) for [stochastic gradient descent](learn_to_code/machine_learning/machine_learning?id=stochastic-gradient-descent)); if set to an int, this is the seed; if None (default) there is no seed.
 * `n_jobs` (not listed) Number of CPU cores used when parallelizing over classes if multi_class=’ovr’”. This parameter is ignored when the solver is set to ‘liblinear’ regardless of whether ‘multi_class’ is specified or not. None means 1 unless in a joblib.parallel_backend context. -1 means using all processors.
* To create the model, use `lr.fit(X_train_std, y_train)`, where 'X_train_std' contains the features in the normalized training data and 'y_train' contains the labels.
 * Note that this seems to be done in place.
* Predictions can be made with `lr.predict_proba(X_test_std)`, where 'X_test_std' contains the features in the normalized data (training or testing). This method returns a list of predictions.
 * This returns one line for each [sample](learn_to_code/machine_learning/ml_key_terms?id=sample)
 * It has m columns, where m = number of unique [labels](learn_to_code/machine_learning/ml_key_terms?id=labels); this is an artifact of scikit-learn using [One-vs.-Rest](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr) automatically. Each column represents the sample's probability of belonging to that specific label (the labels are stored in the variable `lr.classes_`).

After the model is made, there are variables stored in the `lr` object which are of interest. These are:
* **Classes** - `lr.classes_`, which are the labels. 
* **Weights** - `lr.coef_`
* **Bias Weight** - `lr.intercept_`

Code Example:
```
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

from matplotlib.colors import ListedColormap
import matplotlib.pyplot as plt

import numpy as np

def plot_decision_regions(X, y, classifier, test_idx=None, resolution=0.02):
	# setup marker generator and color map
	markers = ('s', 'x', 'o', '^', 'v')
	colors = ('red', 'blue', 'lightgreen', 'gray', 'cyan')
	cmap = ListedColormap(colors[:len(np.unique(y))])
	# plot the decision surface
	x1_min, x1_max = X[:, 0].min() - 1, X[:, 0].max() + 1
	x2_min, x2_max = X[:, 1].min() - 1, X[:, 1].max() + 1
	xx1, xx2 = np.meshgrid(np.arange(x1_min, x1_max, resolution),
						np.arange(x2_min, x2_max, resolution))
	Z = classifier.predict(np.array([xx1.ravel(), xx2.ravel()]).T)
	Z = Z.reshape(xx1.shape)
	plt.contourf(xx1, xx2, Z, alpha=0.4, cmap=cmap)
	plt.xlim(xx1.min(), xx1.max())
	plt.ylim(xx2.min(), xx2.max())
	# plot all samples
	for idx, cl in enumerate(np.unique(y)):
		plt.scatter(x=X[y == cl, 0], y=X[y == cl, 1], alpha=0.8, c=cmap(idx), marker=markers[idx], label=cl)

	# highlight test samples
	if test_idx:
		X_test, y_test = X[test_idx, :], y[test_idx]
		plt.scatter(X_test[:, 0], X_test[:, 1], c='', alpha=1.0, linewidths=1, marker='o', s=55, label='test set')

if __name__ == "__main__":
	
	#load the data, only using the third and fourth columns for the features 
	iris = datasets.load_iris()
	X = iris.data[:, [2, 3]]
	y = iris.target
	
	print "The corresponding label names are: {}".format(iris.get('target_names'))
	
	#make a training and testing data set
	X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)

	#find the parameters necessary for standardization of the training set
	sc = StandardScaler()
	sc.fit(X_train)
	
	#standardize the training data and the test data using the standardization parameters derived from the training data 	
	X_train_std = sc.transform(X_train)
	X_test_std = sc.transform(X_test)
	
	#use LogisticRegression and 'fit' to create the logistic regression model
	lr = LogisticRegression(C=1000.0, random_state=0)
	lr.fit(X_train_std, y_train)
	
	#plot data
	X_combined_std = np.vstack((X_train_std, X_test_std))
	y_combined = np.hstack((y_train, y_test))
	plot_decision_regions(X_combined_std, y_combined, classifier=lr, test_idx=range(105,150))
	plt.xlabel('petal length [standardized]')
	plt.ylabel('petal width [standardized]')
	plt.legend(loc='upper left')
	plt.show()
	
	#show the probabilities of the samples for each class; recall that scikit-learn automatically uses OvR, so this means the probability it belongs to each 
	#class can be returned.
	testPredictions = lr.predict_proba(X_test_std)
	
	print "The first 3 samples are classified as {}".format(testPredictions[:3,:])
	print "The classes are {}".format(lr.classes_)
	print "The weights are {}".format(lr.coef_)
	print "The bias weight is {}".format(lr.intercept_)
```

This will print:
```
The corresponding label names are: ['setosa' 'versicolor' 'virginica']
The first 3 samples are classified as [[2.05743774e-11 6.31620264e-02 9.36837974e-01]
 [6.08753106e-04 9.99285569e-01 1.05678028e-04]
 [8.16962653e-01 1.83037347e-01 6.48299403e-14]]
The classes are [0 1 2]
The weights are [[-7.34015187 -6.64685581]
 [ 2.54373335 -2.3421979 ]
 [ 9.46617627  6.44380858]]
The bias weight is [-9.31757401 -0.89462847 -8.85765974]
```

Note that there are 3 groupings of weights (as well as 3 weight biases). This is because scikit-lwearn uses [One-vs.-Rest](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr), and there are 3 classes in this dataset. There are 3 groups of weights because the first set identifies 'setosa' (0) vs everything else, the second identifies 'versicolor' (1) vs everything else, and the last identifies 'virginica' (2) vs everything else. The first 3 sets of probabilities are:
```
[[2.05743774e-11 6.31620264e-02 9.36837974e-01]
 [6.08753106e-04 9.99285569e-01 1.05678028e-04]
 [8.16962653e-01 1.83037347e-01 6.48299403e-14]]
```

From this, we can say that the first sample (with probabilitiy p=93.683%) is of type 'virginica', the second sample (with probabilitiy p=99.929%) is of type 'versicolor', and the third sample (with probabilitiy p=81.696%) is of type 'setosa'.

 
**<font size="4">Showing How To Calculate Predictions Without 'predict_proba()'</font>**

If you want to see how the actual prediction is made (for [binary classification](learn_to_code/machine_learning/ml_key_terms?id=binary-classification) only), this is how it can be done in Python; this is [how to formulate the prediction](learn_to_code/machine_learning/logistic_regression?id=making-the-prediction) yourself (using the weights from the LogisticRegression package). Please note: this example limits the number of available classifiers, as using multiple classes for this exercise must be approached differently.

> This example uses a [Scikit-learn dataset](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-datasets), [Scikit-learn train/test data split](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-traintest-data-split), and [normalization](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-normalization).

```
from sklearn import datasets
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression

import numpy as np

if __name__ == "__main__":
	#Load the dataset
	iris = datasets.load_iris()
	
	#restrict the dataset to just two classes (0 and 1); there is also a class '2' in the data, but using multiple classes for this exercise needs a different approach
	#(one that cannot be easily shown); so limit to just classes [0, 1] 
	numpyLogic = (iris.target[:]<2)
	y_mod = iris.target[numpyLogic]
	x_mod = iris.data[:, [2, 3]]
	x_mod = x_mod[numpyLogic]
	
	#split into train and test data
	X_train, X_test, y_train, y_test = train_test_split(x_mod, y_mod, test_size=0.3, random_state=0)
	
	#standardize using training data
	sc = StandardScaler()
	sc.fit(X_train)
	
	#actually standardize the training data using the standardization parameters derived from the training data 	
	X_train_std = sc.transform(X_train)	
	
	#run the logistic regression - make the logistic regression model
	lr = LogisticRegression(C=1000.0, random_state=0)
	lr.fit(X_train_std, y_train)	
	
	#find z by taking the dot product of the coefficients (aka weights) and the transpose of the training data; then, add the intercept (aka the bias weight) to each
	z = (lr.coef_.dot(X_train_std[:,:].T) + lr.intercept_)
	
	#now that we have z, determine the probability that each sample is from the class '1'
	manualPredictPositive = 1/(1+np.exp(-z))
	
	#take 1-manualPredictPositive to determine the probability of belonging to class '0'
	manualPredictNegative = 1-manualPredictPositive
	
	#build numpy array 'manualPredict', where the first column tells us the probability of belonging to class '0' and the second 
	#tells us the probability of belonging to class '1' 
	manualPredict = np.zeros([X_train_std.shape[0], 2], dtype = float)
	manualPredict[:,0] = manualPredictNegative
	manualPredict[:,1] = manualPredictPositive 
	
	#print the first 7 probabilities as predicted by the official method 'predict_proba', then print the first 7 entries of our manual job.
	#if they are the same, we did this correctly! 
	print lr.predict_proba(X_train_std)[:7,:]
	print "XXXXXXXXXXXXXXXXXXX"
	print manualPredict[:7,:]
```

# Scikit-learn: Model Evaluation

## Scikit-learn: Accuracy

Simple accuracy of an entire dataset can be obtained by importing `from sklearn.metrics import accuracy_score` and then using the function `accuracy_score(y, y_pred)`, where 'y' is a 1d numpy array that houses the [ground truth](learn_to_code/machine_learning/ml_key_terms?id=ground-truth) of the data and 'y_pred' is a 1d numpy array that houses the predictions.

This simply returns a scalar that denotes accuracy. An example of it's use can be found [here](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-perceptron).

!> Accuracy, by itself, is usually not good for evaluating a model in machine learning.

# Examples

## Artificial Neuron (Perceptron)

!> This example does **not** use a package to do the work - its all coded!

This is an example of an [Artificial neuron (Perceptrons)](/learn_to_code/machine_learning/perceptron) - one of the first machine learning algorithms. It uses the Iris dataset ('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data'), which you can directly read from the internet to a pd.read_csv. 

This example is found in the PACKT book 'Python Machine Learning' by Sebastian Raschka, chapter 2, pages 18-32.

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt


class Perceptron(object):
	"""Perceptron classifier.
	Parameters
	------------
	learningRate (float) Learning rate (between 0.0 and 1.0)
	n_iter (int) Passes over the training dataset.
	
	Attributes
	-----------
	w_ (1d-array) Weights after fitting.
	errors_ (list) Number of misclassifications in every epoch.
	"""
	
	def __init__(self, learningRate=0.01, n_iter=10):
		self.learningRate = learningRate
		self.n_iter = n_iter
	
	def fit(self, X, y):
		"""Fit training data.
		Parameters
		----------
		X : {array-like}, shape = [n_samples, n_features] Training vectors, where n_samples is the number of samples and n_features is the number of features.
		y : array-like, shape = [n_samples] Target values.
		"""
		
		#######Bias 
		#Note that we actually add one weight to the beginning - this is called the bias.
		#Its not explained in the book, but is sort of explained here:
		#https://stackoverflow.com/questions/2480650/role-of-bias-in-neural-networks
		#Just know that the 'feature' is always a constant 1
		#######Bias
		
		#we add one slot to the weights, as apparently there is something needed for the first slot
		self.w_ = np.zeros(1 + X.shape[1])
		self.errors_ = []
		
		
		print type(X)
		print type(y)
		for _ in range(self.n_iter):
			errors = 0
			for xi, target in zip(X, y):
				"""
				Here we figure out the update (which is (the learning rate) * (the error)) and then multiply it by the value,  
				then finally adding that to the weight.
				If there is no error, the 'update' will be 0, which means the weight wont change at all!
				
				Also note the 'bias' (https://stackoverflow.com/questions/2480650/role-of-bias-in-neural-networks) weight is added
				in the beginning (so its self.w_[0]), and its 'feature' is a constant 1 (which is why we simply add the update) 
				
				The weight bias appears to be updated with the error.
				"""
				update = self.learningRate * (target - self.predict(xi))
				self.w_[1:] += update * xi
				self.w_[0] += update
				errors += int(update != 0.0)
			self.errors_.append(errors)
			
		print self.w_
		return self

	def net_input(self, X):
		"""Calculate net input - this is simply a substep of making the prediction. It may even be possible to roll this into the prediction, but it is left out as 
		to illustrate this is the input to the prediction function."""
		return np.dot(X, self.w_[1:]) + self.w_[0]
	
	def predict(self, X):
		"""Return class label after unit step"""
		return np.where(self.net_input(X) >= 0.0, 1, -1)

if __name__ == "__main__":
	df = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', header=None)
	
	#Here, we convert the y to be either -1 or 1; -1 is where the flower is 'Iris-setosa', and a 1 if not (in other wirds, this is simply a binary choice)
	#also note we are only taking the first 100
	y = df.iloc[0:100, 4].values
	y = np.where(y == 'Iris-setosa', -1, 1)
	
	X = df.iloc[0:100, [0, 2]].values

	plt.scatter(X[:50, 0], X[:50, 1],color='red', marker='o', label='setosa')
	plt.scatter(X[50:100, 0], X[50:100, 1], color='blue', marker='x', label='versicolor')
	plt.xlabel('sepal length')
	plt.ylabel('plearningRatel length')
	plt.legend(loc='upper left')
	plt.show()
	
	
	ppn = Perceptron(learningRate=0.1, n_iter=10)
	ppn.fit(X, y)
	plt.plot(range(1, len(ppn.errors_) + 1), ppn.errors_, marker='o')
	plt.xlabel('Epochs')
	plt.ylabel('Number of misclassifications')
	plt.show()
```

## Adaline

!> This example does **not** use a package to do the work - its all coded!

This is an example of [Adaline](/learn_to_code/machine_learning/adaline) (ADAptive LInear NEuron) - one of the first machine learning algorithms, which uses [gradient desent](learn_to_code/machine_learning/machine_learning?id=gradient-descent). It uses the Iris dataset ('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', which you can directly read from the internet to a pd.read_csv).


This example is found in the PACKT book 'Python Machine Learning' by Sebastian Raschka, chapter 2, pages 33-45.

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from mlxtend.plotting import plot_decision_regions

class AdalineGD(object):
	"""ADAptive LInear NEuron classifier.
	Parameters
	------------
	learningRate : float
	Learning rate (between 0.0 and 1.0)
	n_iter : int
	Passes over the training dataset.
	Attributes
	-----------
	w_ : 1d-array
	Weights after fitting.
	errors_ : list
	Number of misclassifications in every epoch.
	"""

	def __init__(self, learningRate=0.01, n_iter=50):
		self.learningRate = learningRate
		self.n_iter = n_iter
		
	def fit(self, X, y):
		""" Fit training data.
		Parameters
		----------
		X : {array-like}, shape = [n_samples, n_features]
		Training vectors,
		where n_samples is the number of samples and
		n_features is the number of features.
		y : array-like, shape = [n_samples]
		Target values.
		Returns
		-------
		
		self : object
		"""
		self.errors_ = []
		
		self.w_ = np.zeros(1 + X.shape[1])
		self.cost_ = []
		for i in range(self.n_iter):
			"""
			This function differs from the Perceptron in a few ways:
			1. The Perceptron updates the weights after each sample, but this updates the weights in batch (after each epoch).
			2. The Perceptron calculates the label (1 or -1) before modifying the weight update value, but this leaves that number raw (also its called the 'output' below and not update)
			
			Also note the 'bias' (https://stackoverflow.com/questions/2480650/role-of-bias-in-neural-networks) weight is added
			in the beginning (so its self.w_[0]), and its 'feature' is a constant 1 (which is why we simply add the update).
				
			The weight bias appears to be updated with the error. 
							
			Steps:
			1. Create a weight vector, equal to the number of features (plus one, as you will add a bias weight in the beginning of the vector)
			2. For each epoch:
				A. Get 'net input' by finding the dot product between the features and the weights
				B. Subtract the 'net input' from the ground truths - this is the error
				C. Take the transpose of the features and find the dot product with the errors - then multiply this by the learning rate. Update the weights with this value (do NOT update the bias weight here).
				D. Sum up the errors, multiply by the learning rate, and update the bias weight with this number.
			
			Each epoch has a 'cost', but this seems to be the error rate for the entire epoch
			"""
			output = self.net_input(X)
			errors = (y - output)	
								
			self.w_[1:] += self.learningRate * X.T.dot(errors)
			self.w_[0] += self.learningRate * errors.sum()
			cost = (errors**2).sum() / 2.0
			self.errors_.append(errors)
			self.cost_.append(cost)
							
		return self
	
	def net_input(self, X):
		"""Calculate net input - this is a substep of making the prediction. It is also used directly from 'fit' (the Perceptron converted the dot product to the labels, but Adaline does not).
		This is directly called from both 'predict' and 'fit' (unlike the Perception, which only called this from 'predict').
		
		All that said, this function is the same in the Perceptron as it is here."""
		return np.dot(X, self.w_[1:]) + self.w_[0]
	
	def activation(self, X):
		"""Compute linear activation - It may even be possible to eliminate this and just call 'net_input(X)' directly from 'predict', but it is left 
		to illustrate this is the input to the prediction function."""
		return self.net_input(X)
	
	def predict(self, X):
		"""Return class label after unit step"""
		return np.where(self.activation(X) >= 0.0, 1, -1)

if __name__ == "__main__":
	
	df = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', header=None)
	
	#Here, we convert the y to be either -1 or 1; -1 is where the flower is 'Iris-setosa', and a 1 if not (in other wirds, this is simply a binary choice)
	#also note we are only taking the first 100
	y = df.iloc[0:100, 4].values
	y = np.where(y == 'Iris-setosa', -1, 1)
	
	X = df.iloc[0:100, [0, 2]].values
	
	fig, ax = plt.subplots(nrows=1, ncols=2, figsize=(8, 4))
	ada1 = AdalineGD(n_iter=10, learningRate=0.01).fit(X, y)
	ax[0].plot(range(1, len(ada1.cost_) + 1), np.log10(ada1.cost_), marker='o')
	ax[0].set_xlabel('Epochs')
	ax[0].set_ylabel('log(Sum-squared-error)')
	ax[0].set_title('Adaline - Learning rate 0.01')
	ada2 = AdalineGD(n_iter=10, learningRate=0.0001).fit(X, y)
	ax[1].plot(range(1, len(ada2.cost_) + 1), ada2.cost_, marker='o')
	ax[1].set_xlabel('Epochs')
	ax[1].set_ylabel('Sum-squared-error')
	ax[1].set_title('Adaline - Learning rate 0.0001')
	plt.show()
	
	#Above we noticed that a learning rate of .01 made the model actually get worse, and .0001 would take too long to converge; if we normalize all values (via standardization) 
	#we can use larger learning rates
	
	#normalize all features with standardization
	X_std = np.copy(X)
	X_std = (X - X.mean(axis=0)) / X.std(axis=0)
	
	ada = AdalineGD(n_iter=15, learningRate=0.01)
	ada.fit(X_std, y)
	plot_decision_regions(X_std, y, clf=ada)
	plt.title('Adaline - Gradient Descent')
	plt.xlabel('sepal length [standardized]')
	plt.ylabel('petal length [standardized]')
	plt.legend(loc='upper left')
	plt.show()
	plt.plot(range(1, len(ada.cost_) + 1), ada.cost_, marker='o')
	plt.xlabel('Epochs')
	plt.ylabel('Sum-squared-error')
	plt.show()
```

## Adaline (Stochastic GD)

This is an example of Adaline (ADAptive LInear NEuron) - one of the first machine learning algorithms. It differs from the [other version on Adaline](learn_to_code/python/scipy/machine_learning_in_python?id=adaline) in the sense that
this uses [stochastic gradient descent](learn_to_code/machine_learning/machine_learning?id=stochastic-gradient-descent), whereas the other example simply used [gradient desent](learn_to_code/machine_learning/machine_learning?id=gradient-descent).

Also, stochastic gradient descent requires the data to be randomized after each epoch - so that is done in this code too.
 
This example uses the Iris dataset ('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', which you can directly read from the internet to a pd.read_csv).
This example is found in the PACKT book 'Python Machine Learning' by Sebastian Raschka, chapter 2, pages 42-47.

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from mlxtend.plotting import plot_decision_regions
from numpy.random import seed

class AdalineSGD(object):
	"""ADAptive LInear NEuron classifier.
	Parameters
	------------
	eta : (float) - Learning rate (between 0.0 and 1.0)
	n_iter : (int) - Passes over the training dataset.

	Attributes
	-----------
	w_ : (1d-array) - Weights after fitting.
	errors_ : (list) - Number of misclassifications in every epoch.
	shuffle : (bool (default: True)) - Shuffles training data every epoch if True to prevent cycles.
	random_state : (int (default: None)) - Set random state for shuffling and initializing the weights.
	"""
	
	def __init__(self, eta=0.01, n_iter=10, shuffle=True, random_state=None):
		self.eta = eta
		self.n_iter = n_iter
		self.w_initialized = False
		self.shuffle = shuffle

		if random_state:
			seed(random_state)
			
	def fit(self, X, y):
		""" Fit training data.
		Parameters
		----------
		X : ({array-like}, shape = [n_samples, n_features]) - Training vectors, where n_samples is the number of samples and n_features is the number of features.
		y : (array-like, shape = [n_samples]) - Target values.
		
		Returns
		-------
		self : object"""

		#initialize the weights and cost
		self._initialize_weights(X.shape[1])
		self.cost_ = []
		
		#cycle through each epoch
		for _ in range(self.n_iter):
			
			#IF the training data is to be shuffled - and since we are using stocahastic gradient descent (i.e. we update the weights after each sample), the data MUST be 
			#shuffled after each epoch
			if self.shuffle:
				X, y = self._shuffle(X, y)
			
			#re-set the cost for this epoch
			cost = []
			
			for xi, target in zip(X, y):
				#update the weight with this sample
				cost.append(self._update_weights(xi, target))
				
			#get the average cost, which will be this epoch's cost value
			avg_cost = sum(cost)/len(y)
			
			#add this epoch's cost value to the cost list
			self.cost_.append(avg_cost)
		return self
	
	def partial_fit(self, X, y):
		"""Fit training data without reinitializing the weights
		
		Partial fits are for continuously updating the data (like people entering data into a website, or live data) - this will update the weights as appropriate
		
		Notice there is no 'cost' evaluation for this, as there are no epochs 
		"""
		
		#if the weights have not been initialized, do so
		if not self.w_initialized:
			self._initialize_weights(X.shape[1])
		
		#check to see if the y is one or many - if its just one we can send that directly to the 'self._update_weights()' method - otherwise, we will have to sue a for loop 
		if y.ravel().shape[0] > 1:
			for xi, target in zip(X, y):
				self._update_weights(xi, target)
		else:
			self._update_weights(X, y)
		return self
	
	def _shuffle(self, X, y):
		"""Shuffle training data"""
		
		r = np.random.permutation(len(y))
		return X[r], y[r]
	
	def _initialize_weights(self, m):
		"""Initialize weights to zeros"""
		
		#initialize the weights, adding 1 for the bias weight
		self.w_ = np.zeros(1 + m)
		self.w_initialized = True
		
	def _update_weights(self, xi, y):
		"""Apply Adaline learning rule to update the weights
		
		To update an individual weight:
		1. Find the prediction based on xi
		2. find the error
		3. Update the weights by multiplying the learning rate (a scalar) with the error (a scalar) and xi (a vector of the features), then adding this to each weight.
		4. Update the bias weight by multiplying the learning rate (a scalar) with the error (a scalar) and 1 (which is always a constant for the bias weight), 
			then adding this to the bias weight.
		5. Calculate the Sum of Squared Errors (SSE) and consider that the 'cost' function
		6. Return the cost function value 
		"""
		
		output = self.net_input(xi)
		error = (y - output)
		self.w_[1:] += self.eta * xi.dot(error)
		self.w_[0] += self.eta * error
		cost = 0.5 * error**2
		return cost
	
	def net_input(self, X):
		"""Calculate net input - this is a substep of making the prediction. It is also used directly from 'fit' (the Perceptron converted the dot product to the labels, but Adaline does not).
		This is directly called from both 'predict' and 'fit' (unlike the Perception, which only called this from 'predict').

		All that said, this function is the same in the Perceptron as it is here."""
		return np.dot(X, self.w_[1:]) + self.w_[0]
	
	def activation(self, X):
		"""Compute linear activation - It may even be possible to eliminate this and just call 'net_input(X)' directly from 'predict', but it is left 
		to illustrate this is the input to the prediction function."""
		return self.net_input(X)
	
	def predict(self, X):
		"""Return class label after unit step"""
		return np.where(self.activation(X) >= 0.0, 1, -1)

if __name__ == "__main__":
	
	df = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', header=None)
	
	#Here, we convert the y to be either -1 or 1; -1 is where the flower is 'Iris-setosa', and a 1 if not (in other wirds, this is simply a binary choice)
	#also note we are only taking the first 100
	y = df.iloc[0:100, 4].values
	y = np.where(y == 'Iris-setosa', -1, 1)
	
	X = df.iloc[0:100, [0, 2]].values
	
	#normalize all features with standardization
	X_std = np.copy(X)
	X_std = (X - X.mean(axis=0)) / X.std(axis=0)	
	
	ada = AdalineSGD(n_iter=15, eta=0.01, random_state=1)
	ada.fit(X_std, y)
	plot_decision_regions(X_std, y, clf=ada)
	plt.title('Adaline - Stochastic Gradient Descent')
	plt.xlabel('sepal length [standardized]')
	plt.ylabel('petal length [standardized]')
	plt.legend(loc='upper left')
	plt.show()
	
	plt.plot(range(1, len(ada.cost_) + 1), ada.cost_, marker='o')
	plt.xlabel('Epochs')
	plt.ylabel('Average Cost')
	plt.show()
```