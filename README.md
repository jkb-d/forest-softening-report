# Softening of trees in random forests

This report describes experiments with application of split softening[1]
to the trees in random forests[2] for classification problems with 2 classes.
To compare performance of the models we use the "Area under (ROC) curve" - AUC.
The values of AUC used in comparisons in this report are computed with the
"trapezoid method" using test sets.
Lets call the classes "positive" and "negative".

In the original design of random forests for classification the output of any tree
in the forest is a class label and the result from the forest is the prevailing
label over all trees.
For our purpose of AUC evaluation we need output of the forest in the form of "score" -
a comparable value (typically from the interval [0, 1]) which is higher when
the submitted case is more likely classified as "positive".
To achieve this we modify the way how the random forest model
produces the output for a data case:
Each tree provides its score which is 1 when it outputs label "positive"
and 0 when the output is label "negative".
These scodes are averaged over all trees to produce the forest score.
When trees in the forest are softened, the score from each tree is
in the interval [0, 1].

Experiments are implemented in R with packages [randomForest][https://cran.r-project.org/web/packages/randomForest/]
and [SplitSoftening][https://cran.r-project.org/web/packages/SplitSoftening/].
With the package randomForest a forest with 200 trees is grown using a training set.
The trees from the forest are converted to the form used by the package SplitSoftening
and then a softening method is applied.
Values of AUC are computed with the test set using the forest before and after softening.

We want to observe results from non-softened and softened forests depending on
forest size (number of trees) and also how it is influenced by the parameter
"sampsize" which specifies the size of samples drawn (with replacement)
from the whole training set to grow each tree in the forest.
Thus from each training set we prepare forests with several values of the "sampsize" parameter.
To get results with various forest sizes *n*=10, 20, 30, ..., 200
we use for prediction only first *n* trees from the forest.

When applying a softening method to each tree in the forest, the whole training set
is used - the same set as used for training the forest.
Although each tree from the forest is grown with its own sample from this training set,
it does not make sense to use this sample for softening with optimization method,
because the trees in the random forest are grown to the maximal size, which means
that when the sample from the training set is evaluated with the tree,
all the cases are classified with no error
(to any leaf fall cases only from one class).
From this it follows, that "zero softening" - the softening which is equivalent
to non-soft tree -
is optimal for this sample of training cases.


[1]: Dvořák, Jakub: *Classification trees with soft splits optimized for ranking*,
Computational Statistics (2019) 34:763–786.

[2]: Breiman, Leo: *Random Forests*, Machine Learning (2001) 1:5-32.
