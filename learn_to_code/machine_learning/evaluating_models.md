# Evaluating Models

# Basics


# Correlation

> A good resource for correlation can be found [here](https://link.springer.com/article/10.1057%2Fjt.2009.5)

Correlation shows a dependence or an association between two data sets; usually we think of correlation referring to how close two variables are to having a linear relationship.

!> Correlation may be causal, or it may not be

## Correlation in Machine Learning

!> Correlation is not actually a fantastic measure to evaluate a model - the RMSE is much better at that.  That said, correlation can be used to see if a [feature](learn_to_code/machine_learning/machine_learning?id=key-terms-for-the-data) is a good candidate to be included in the model.

Corellation acts a bit differently in a machine learning setting (as opposed to other statistical uses). For example, a correlation of just under .5 may not be particularly useful for other applications; however, as far as machine learning is concerned, a ~.5 corrleation is desirable. Keep in mind that we do not use just _one_ feature to make our prediction: we use a combination of dozens (or more) of features. Its the _combination_ of features (and indirectly, their correlations) that determines the success (or failure) of a machine learning model.

Correlating the prediction vs the [ground truth](learn_to_code/machine_learning/machine_learning?id=key-terms-for-the-data) can sometimes be used to determine the effectiveness of the model; that said, if this is used, its also a good idea to check the RMSE as well.

# Sum of Squared Errors (SSE)

SSE is one of the older, non-correlation methods to evaluate a machine learning model.

SSE is used as a cost function to determine the validity of a model. The equation is `sum((ground truth - predictions)^2)/2`. We divide by two simply to make it easier to derive the gradient.

The goal is to get the SSE as close to 0 as possible.