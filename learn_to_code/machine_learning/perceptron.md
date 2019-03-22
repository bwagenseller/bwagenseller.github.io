# Perceptron

# Source Material

## Python Machine Learning (PACKT Book)

The 'Python Machine Learning' book by PACKT (Sebastian Raschka) helped me develop an idea on how perceptrons work.

# Unorganized Notes

* The perceptron was the earliest machine learning algorithm.
* Works by
 * Taking features, and making weights for each feature (plus a [bias](https://stackoverflow.com/questions/2480650/role-of-bias-in-neural-networks) weight in the beginning with its pseudo-'feature' is a constant 1).
 * The weights start at 0, but are updated after each row is predicted with the weights.
* Data MUST be [linearly separable](https://en.wikipedia.org/wiki/Linear_separability)! The only wany to converge if its not is by issuing a max number of [epochs](learn_to_code/machine_learning/machine_learning?id=key-terms-for-the-data).
 * Realistically, Perceptrons are not used in industry because of the above.

