# Key Terms of Machine Learning

# Batch

When [weights](learn_to_code/machine_learning/ml_key_terms?id=weight) are updated after an entire [epoch](learn_to_code/machine_learning/ml_key_terms?id=epoch) is completed, this is called a **batch** update (as opposed to updating after every row of training data, which [on-line updating](learn_to_code/machine_learning/ml_key_terms?id=on-line-updating) does).

# Bias (Weight)

The bias (for [weights](learn_to_code/machine_learning/ml_key_terms?id=weight)) is an additional weight that is added to the front of the weight array (actually, weights are usually vectors). For example, if there are, say, 11 features, there will also be 11 weights (initialized to 0 or a small number); in some cases there will be 12 weights to accomodate the bias. Usually, a [model](learn_to_code/machine_learning/machine_learning?id=machine-learning-model) is built with weights, so future predictions simply have to multiply the weights and features in a dot matrix as part of (but not all) of the prediction; since there is no associated 'feature' with the bias weight, we simply use the number 1 in place of a 'feature' (or, more precisely, a vector filled with ones).

There is us explained more [in this stackoverflow article](https://stackoverflow.com/questions/2480650/role-of-bias-in-neural-networks).

# Binary Classification

**Binary Classification** refers to a [classification](learn_to_code/machine_learning/machine_learning?id=classification) that has only two choices (usually \[-1, 1\] or \[0, 1\]). Most machine learning algorithms that perform classification can only handle binary classification; that said, its possible to get around this limitation when using the [One-vs.-Rest (OvR)](learn_to_code/machine_learning/machine_learning?id=one-vs-rest-ovr) approach ([scikit-learn does this automatically](learn_to_code/python/scipy/machine_learning_in_python?id=scikit-learn-one-vs-rest) for most algorithms).

# Classification

See [classification](learn_to_code/machine_learning/machine_learning?id=classification).

# Cost Function

A **cost function** (also sometimes thought of as error) is a way to [evaluate the effectiveness of the model](learn_to_code/machine_learning/evaluating_models). We usually want to minimize this for the best possible model.

# Epoch

The **epoch** is a measurement of the number of times a training dataset is traversed to build the [model](learn_to_code/machine_learning/machine_learning?id=machine-learning-model); so for example, if we iterate through the entire training dataset 3 times to build the model, this is 3 epochs.

# Factors

Pease see [features](learn_to_code/machine_learning/ml_key_terms?id=features).

# Features

**features** or **factors** is the data with which we will make the predictions; these can be thought of as 'columns' in an Excel spreadsheet that contains the dataset we will use.

Features describe the data, but are _not_ labels.

# Gradient Descent

**Gradient Descent** is an optimization algorithm that can be used to find the [weights](learn_to_code/machine_learning/ml_key_terms?id=weight) that minimize the [cost function](learn_to_code/machine_learning/ml_key_terms?id=cost-function) (when said cost function is convex). Gradient Descent uses [batch updates](learn_to_code/machine_learning/ml_key_terms?id=batch) to update its weights.

For more info, please see [the gradient descent info on the machine learning page](learn_to_code/machine_learning/machine_learning?id=gradient-descent).

_See Also:_ [stochastic gradient descent](learn_to_code/machine_learning/ml_key_terms?id=stochastic-gradient-descent)


# Ground Truth

The **ground truth** is the _actual_ classification of the data. The ground truth is used in evaluating how well the [model](learn_to_code/machine_learning/machine_learning?id=machine-learning-model) works on both the in-sample (training data) and out of sample (the testing data) sets, as we can compare the prediction made vs the 'actual' ground truth.

# Hyperparameters

**Hyperparameters** are variables that you can control for a specific machine learning algorithm. Usually these include the [epoch](learn_to_code/machine_learning/ml_key_terms?id=epoch) and the [learning rate](learn_to_code/machine_learning/ml_key_terms?id=learning-rate), but there can be more for a given machine learning algorithm.

# Intercept

See [bias (weight)](learn_to_code/machine_learning/ml_key_terms?id=bias-weight).

# Iterative Updating

See [on-line updating](learn_to_code/machine_learning/ml_key_terms?id=on-line-updating).

# Labels

**Label** (or **y**) is the set of the data that we are trying to predict. y is known to be concretely correct and should be provided with the overall dataset (at least for our purposes of building a [model](learn_to_code/machine_learning/machine_learning?id=machine-learning-model) - obviously it will not be present for data you are actually trying to predict in the real world). When we are making the model this data is necessary; when we are trying to make a prediction on data we do not know how to classify this is obviously not necessary as we would not have it. This is sometimes called the _y_.

# Learning Rate

The **learning rate** is a multiplier that determines how much update variables (usually [weights](learn_to_code/machine_learning/ml_key_terms?id=weight), but not always) are updated. The learning rate is between 0 (no learning at all) and 1 (every new item learned is fully recorded).

The learning rate can vary widely; for example, a learning rate of .01 may be too large, as you may end up continuously overshooting the global (or even local) minimum and you are not minimizing the cost function.

In addition, the learning rate can deay over time; this can be referred to as **adaptive learning rate**, and one way to do this is via (c1/((number of iterations) + C2)), where C1 and C2 are constants.

# Local Minima

When using [gradient descent](learn_to_code/machine_learning/ml_key_terms?id=gradient-descent), its possible that there may be many 'low' points that may trick the algorithm into thinking it has hit the global minima, when in fact it has just found a smaller minima. These are known as **local minima**.

# Mini-Batch Learning

**mini-batch learning** is a compromise between [batch updates](learn_to_code/machine_learning/ml_key_terms?id=batch) and [on-line updating](learn_to_code/machine_learning/ml_key_terms?id=on-line-updating); instead of waiting to update the weights after _all_ the training data is presented _or_ after each record, we update after a set number of records (for example, 50).

# On-Line Updating

**On-Line Updating** (or **On-Line Learning**)is the opposite of updating by [batch](learn_to_code/machine_learning/ml_key_terms?id=batch): in on-line updating, the weights are updated after _each_ training example.

# Regression

See [regression](learn_to_code/machine_learning/machine_learning?id=regression).

# Sample

A **sample** is a single element that spans all [features](learn_to_code/machine_learning/ml_key_terms?id=features) in a dataset; it can be thought of a single 'row' in the data. 

# Scalar

A **scalar** is any real number (usually a measurement of some sort).  A scalar is said to have magnitude, but no direction.  Some examples of scalars are: 
* 0
* 3.65
* -1.347
* 2.0

# Stochastic Gradient Descent

**Stochastic Gradient Descent** is very similar to [gradient descent](learn_to_code/machine_learning/ml_key_terms?id=gradient-descent) with one key difference: instead of the weights being updated using [batch updates](learn_to_code/machine_learning/ml_key_terms?id=batch), they are updated via [on-line updating](learn_to_code/machine_learning/ml_key_terms?id=on-line-updating).

For more info, please see [the stochastic gradient descent info on the machine learning page](learn_to_code/machine_learning/machine_learning?id=stochastic-gradient-descent).

# Vectorization

**Vectorization** (or **vectorized code**) usually referrs to using abstracted 'for' loops to accomplish a task (for a sizeable performance boost).  **Vectorization** comes into play mostly for interpreted languages (such as Python, R, etc) which are not as efficeint as a compiled languague (e.g. the 'C' language) that is optimized for the specific task. 

An example is Pandas (Python) [dataFrames](learn_to_code/python/scipy/pandas?id=dataframes).  Lets say we had a dataframe `someDataFrame` and a column `col_A`, and lets say we wanted to make all values of this column = -1.  We could do this in Python/Pandas:
```
someDataFrame['col_A'] = -1
```  

This will go through _all_ rows of the dataframe and set column `col_A` = -1.  You may think 'that could easily be done in a 'for' loop in Python', and it could, but the problem is the 'for' loop in Python is on some spectrum of the interpreted language scale (meaning its far slower than a truly compiled language) as well as its not optimized.  How **vectorized** code works in this example is there is back-end 'for' loop written in a compiled language (usually the 'C' language) that is optimized to accomplish this task. The back-end 'for' loop in the compiled language is _far_ faster than the interpreted language's 'for' loop implementation.  

**Vectorized** code can be read similarly to SQL, in a sense, as SQL also performs operations that do not directly name a 'for' loop.  

> Usually, languagues like Python and R benefit the most from vectorization, but even compiled languages can too (particularly with readability of code).  

# Weight

**Weight** is simply a term that modifies the significance of a feature; there are as many weights as there are [features](learn_to_code/machine_learning/ml_key_terms?id=features), with an exception of sometimes also including a [bias weight](learn_to_code/machine_learning/ml_key_terms?id=bias-weight).

Weights are usually initialized to 0 (or a small number) and are incrementally changed - via [batch updating](learn_to_code/machine_learning/ml_key_terms?id=batch) or [on-line updating](learn_to_code/machine_learning/ml_key_terms?id=on-line-updating) - over time, in proportion to the [learning rate](learn_to_code/machine_learning/ml_key_terms?id=learning-rate). How this update process takes place depends on the machine learning algorithm.

# With/Out Replacement

The terms **with replacement** and **without replacement** refer to sampling random samples in the data. **With replacement** means if a sample is selected randomly it is kept in the dataset to potentially be used again; **without replacement** means the sample is removed from the dataset once it is used.

# Y

Pease see [labels](learn_to_code/machine_learning/ml_key_terms?id=labels).

# Y Prime
**y'** (y prime) is a set of results that are truly predictions; whereas [**y**](learn_to_code/machine_learning/ml_key_terms?id=labels) is a set of _known_ labels or results, the **y'** set of data is usually obtained by running [testing data](learn_to_code/machine_learning/machine_learning?id=testing-data) through a [model](learn_to_code/machine_learning/machine_learning?id=machine-learning-model); these are predictions that have been made by your model. **y** and **y'** can be used (usually y - y') in part to [evaluate the effectiveness of the model](learn_to_code/machine_learning/evaluating_models).