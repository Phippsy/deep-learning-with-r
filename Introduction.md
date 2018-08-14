Introduction to Deep Learning
================

Element Structure
-----------------

Matrices

``` r
x <- matrix(rep(0, 3*5), nrow = 3, ncol = 5)
x
```

    ##      [,1] [,2] [,3] [,4] [,5]
    ## [1,]    0    0    0    0    0
    ## [2,]    0    0    0    0    0
    ## [3,]    0    0    0    0    0

3d / higher-dimensional *tensors*

``` r
y <- array(rep(0, 2*3*4), dim = c(2,3,4))
y
```

    ## , , 1
    ## 
    ##      [,1] [,2] [,3]
    ## [1,]    0    0    0
    ## [2,]    0    0    0
    ## 
    ## , , 2
    ## 
    ##      [,1] [,2] [,3]
    ## [1,]    0    0    0
    ## [2,]    0    0    0
    ## 
    ## , , 3
    ## 
    ##      [,1] [,2] [,3]
    ## [1,]    0    0    0
    ## [2,]    0    0    0
    ## 
    ## , , 4
    ## 
    ##      [,1] [,2] [,3]
    ## [1,]    0    0    0
    ## [2,]    0    0    0

`y` has:

-   3 axes - rows, columns, depth.
-   Shape 2,3,4
-   Data type double (`typeof(y)`).

A simple example with code
--------------------------

``` r
library(keras)
mnist <- dataset_mnist()
train_images <- mnist$train$x
train_image <- array_reshape(train_images, c(60000, 28 * 28))
train_images <- train_images / 255

test_images <- mnist$test$x
test_image <- array_reshape(test_images, c(10000, 28 * 28))
test_images <- test_images / 255
```

The image data is stored in arrays of 60k x 28 x 28 (train) and 10k x 28 x 28 (test).

We built a network:

``` r
network <- keras_model_sequential() %>% 
  layer_dense(units = 512, activation = "relu", input_shape = c(28*28)) %>% 
  layer_dense(units = 10, activation = "softmax")
```

The network has 2 simple layers - each applying simple tensor operations, which both have weight tensors. Weight tensors are attributes of the layers and are where the **knowledge** of the network persists.

Finally, we compile the neural network:

``` r
network %>% compile(
  optimizer = "rmsprop",
  loss = "categorical_crossentropy",
  metrics = c("accuracy")
)
```

`categorical_crossentropy` is the loss function used as a feedback signal for learning the weight tensors. This is what the training function will attempt to minimize.

The reduction of loss happens via mini-batch stochastic gradient descent. The exact rules governing the use of gradient descent are defined by the `rmsprop` optimizer passed as the first argument.

Finally, we ran the training loop:

``` r
network %>% fit(train_images, train_labels, epochs = 5, batch_size = 128)
```

When we call `fit()`, the network iterates on the training data in mini-batches of size 128 samples, 5 times over (5 epochs). For every iteration, the network calculates the gradients of the weights with regards to the loss on the batch, and updates the weights accordingly. After 5 epochs, the network will have performed 2,345 gradient updates (469 per epoch).

In summary
----------

-   Learning means finding a combination of model parameters which minimise a loss function. It happens by drawing random samples, computing the gradient of the network parameters with respect to loss on the batch. Network parameters are moved a bit with each iteration.
-   Neural networks are chains of tensor operations, meaning that we can apply the chain rule of derivation to find the gradient function mapping the current parameters and current batch of data to a gradient value.
-   *loss* and *optimizers* are 2 key things you'll need to define before starting a network.
-   *loss* is the quantity you want to minimize in training, so it should represent a measure of success.
-   *optimiser* specifies exactly how the gradient of the loss will be used to update parameters.
