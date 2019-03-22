# Adaline

## PACKT - Python Machine Learning

Some of the material here was obtained from the PACKT book 'Python Machine Learning' by Sebastian Raschka.



ADAptive LInear NEuron

Notes:
* Similar to the [Perceptron](/learn_to_code/machine_learning/perceptron), with these differences:
 * The Perceptron updates the weights after each sample, but this updates the weights in batch (after each epoch).
 * The Perceptron calculates the label (1 or -1) before modifying the weight update value, but this leaves that number raw (also its called the 'output' below and not update)
* Steps:  
1\. Create a weight vector, equal to the number of features (plus one, as you will add a bias weight in the beginning of the vector)  
2\. For each epoch:  
&nbsp;&nbsp;&nbsp;&nbsp;A\. Get 'net input' by finding the dot product between the features and the weights  
&nbsp;&nbsp;&nbsp;&nbsp;B\. Subtract the 'net input' from the ground truths - this is the error  
&nbsp;&nbsp;&nbsp;&nbsp;C\. Take the transpose of the features and find the dot product with the errors - then multiply this by the learning rate. Update the weights with this value (do NOT update the bias weight here).  
&nbsp;&nbsp;&nbsp;&nbsp;D\. Sum up the errors, multiply by the learning rate, and update the bias weight with this number.  
* Each epoch has a 'cost', which seems to be the error rate for the entire epoch. This was a new concept introduced by Adaline, as the idea of minimizing a cost function (error) was a new concept at the time.
* A _linear activation function_ is used for learning the weights.
 * This is one of the big differences in Adaline, as it uses the continuous valued output to compute the model error and update the weights, as opposed to the binary class labels.
* A _quantizer_ is used to predict class labels (much like the unit step function in a Perceptron).
* Tiny learning rates are needed (e.x. .0001), _unless_ you [normalize](learn_to_code/machine_learning/machine_learning?id=normalization) the data.