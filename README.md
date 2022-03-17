# Technical report
# Softening of trees in random forests

## Introduction

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

## Experimantal setup

Experiments are implemented in R with packages [randomForest](https://cran.r-project.org/web/packages/randomForest/)
and [SplitSoftening](https://cran.r-project.org/web/packages/SplitSoftening/).
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

We use the following data sets:
- [Magic Telescope](https://archive.ics.uci.edu/ml/datasets/magic+gamma+telescope)
- [MiniBooNE particle identification](https://archive.ics.uci.edu/ml/datasets/MiniBooNE+particle+identification)
- [Waveform](https://archive.ics.uci.edu/ml/datasets/Waveform+Database+Generator+(Version+1))
- [EEG Eye State](https://archive.ics.uci.edu/ml/datasets/EEG+Eye+State)
- [Electrical Grid Stability Simulated Data Data Set](https://archive.ics.uci.edu/ml/datasets/Electrical+Grid+Stability+Simulated+Data+)
- [HTRU2](https://archive.ics.uci.edu/ml/datasets/HTRU2)

The Waveform data set is originally a set with 3 classes.
For the purpose of our experiments we define two problems "wfA" and "wfB",
where "wfA" considers the first class from the original data set as the "signal" class
and the remaining classes as the "background";
the "wfB" consideres the second class as the "signal" and the rest as the "background".
We do not use the data set which has the third class as the "signal" because
it has the same probabilistic distribution as the "wfA" data set.

## Results

In the previous research of split softening in decision trees [1] we use two kinds of softening method:
- without optimization
- with optimization
In the first group the method called "DR1" is identified as the best
from the perspective of performance measured by AUC.
In the second group the method "optim_auc" is the winner;
it uses the Nelder-Mead optimization with the objective function
defined as the AUC computed from the training set.
We use the methods DR1 and optim_auc in our experiments with random forests.

The results are presented in graphs where x-axis is the forest size (number of trees),
y-axis represents AUC computed on the test set.
The main title of the graph contains the data set name, the size of the train set
and the softening method.
There is a subtitle with the value of the "sampsize" parameter.
In each graph there are results from the non-softened forest displayed as black points
and the results from the forest of softened trees as coloured points
(blue for DR1 softening, red for optim_auc).

Using DR1 softening on random forests brings generally no improvement of AUC
on the data sets Magic Telescope and MiniBooNE.

![DR1_magic_00250_00012](img/DR1_magic_00250_00012.png)
![DR1_magic_00250_00025](img/DR1_magic_00250_00025.png)
![DR1_magic_00250_00037](img/DR1_magic_00250_00037.png)
![DR1_magic_00250_00062](img/DR1_magic_00250_00062.png)
![DR1_magic_00250_00100](img/DR1_magic_00250_00100.png)
![DR1_magic_00250_00175](img/DR1_magic_00250_00175.png)
![DR1_magic_00250_00250](img/DR1_magic_00250_00250.png)
![DR1_magic_00500_00025](img/DR1_magic_00500_00025.png)
![DR1_magic_00500_00050](img/DR1_magic_00500_00050.png)
![DR1_magic_00500_00075](img/DR1_magic_00500_00075.png)
![DR1_magic_00500_00125](img/DR1_magic_00500_00125.png)
![DR1_magic_00500_00200](img/DR1_magic_00500_00200.png)
![DR1_magic_00500_00350](img/DR1_magic_00500_00350.png)
![DR1_magic_00500_00500](img/DR1_magic_00500_00500.png)
![DR1_magic_01000_00050](img/DR1_magic_01000_00050.png)
![DR1_magic_01000_00100](img/DR1_magic_01000_00100.png)
![DR1_magic_01000_00150](img/DR1_magic_01000_00150.png)
![DR1_magic_01000_00250](img/DR1_magic_01000_00250.png)
![DR1_magic_01000_00400](img/DR1_magic_01000_00400.png)
![DR1_magic_01000_00700](img/DR1_magic_01000_00700.png)
![DR1_magic_01000_01000](img/DR1_magic_01000_01000.png)
![DR1_magic_02500_00125](img/DR1_magic_02500_00125.png)
![DR1_magic_02500_00250](img/DR1_magic_02500_00250.png)
![DR1_magic_02500_00375](img/DR1_magic_02500_00375.png)
![DR1_magic_02500_00625](img/DR1_magic_02500_00625.png)
![DR1_magic_02500_01000](img/DR1_magic_02500_01000.png)
![DR1_magic_02500_01750](img/DR1_magic_02500_01750.png)
![DR1_magic_02500_02500](img/DR1_magic_02500_02500.png)
![DR1_magic_03750_00187](img/DR1_magic_03750_00187.png)
![DR1_magic_03750_00375](img/DR1_magic_03750_00375.png)
![DR1_magic_03750_00562](img/DR1_magic_03750_00562.png)
![DR1_magic_03750_00937](img/DR1_magic_03750_00937.png)
![DR1_magic_03750_01500](img/DR1_magic_03750_01500.png)
![DR1_magic_03750_02625](img/DR1_magic_03750_02625.png)
![DR1_magic_03750_03750](img/DR1_magic_03750_03750.png)
![DR1_magic_05000_00250](img/DR1_magic_05000_00250.png)
![DR1_magic_05000_00500](img/DR1_magic_05000_00500.png)
![DR1_magic_05000_00750](img/DR1_magic_05000_00750.png)
![DR1_magic_05000_01250](img/DR1_magic_05000_01250.png)
![DR1_magic_05000_02000](img/DR1_magic_05000_02000.png)
![DR1_magic_05000_03500](img/DR1_magic_05000_03500.png)
![DR1_magic_05000_05000](img/DR1_magic_05000_05000.png)

![DR1_mbne_00250_00012](img/DR1_mbne_00250_00012.png)
![DR1_mbne_00250_00025](img/DR1_mbne_00250_00025.png)
![DR1_mbne_00250_00037](img/DR1_mbne_00250_00037.png)
![DR1_mbne_00250_00062](img/DR1_mbne_00250_00062.png)
![DR1_mbne_00250_00100](img/DR1_mbne_00250_00100.png)
![DR1_mbne_00250_00175](img/DR1_mbne_00250_00175.png)
![DR1_mbne_00250_00250](img/DR1_mbne_00250_00250.png)
![DR1_mbne_00500_00025](img/DR1_mbne_00500_00025.png)
![DR1_mbne_00500_00050](img/DR1_mbne_00500_00050.png)
![DR1_mbne_00500_00075](img/DR1_mbne_00500_00075.png)
![DR1_mbne_00500_00125](img/DR1_mbne_00500_00125.png)
![DR1_mbne_00500_00200](img/DR1_mbne_00500_00200.png)
![DR1_mbne_00500_00350](img/DR1_mbne_00500_00350.png)
![DR1_mbne_00500_00500](img/DR1_mbne_00500_00500.png)
![DR1_mbne_01000_00050](img/DR1_mbne_01000_00050.png)
![DR1_mbne_01000_00100](img/DR1_mbne_01000_00100.png)
![DR1_mbne_01000_00150](img/DR1_mbne_01000_00150.png)
![DR1_mbne_01000_00250](img/DR1_mbne_01000_00250.png)
![DR1_mbne_01000_00400](img/DR1_mbne_01000_00400.png)
![DR1_mbne_01000_00700](img/DR1_mbne_01000_00700.png)
![DR1_mbne_01000_01000](img/DR1_mbne_01000_01000.png)
![DR1_mbne_02500_00125](img/DR1_mbne_02500_00125.png)
![DR1_mbne_02500_00250](img/DR1_mbne_02500_00250.png)
![DR1_mbne_02500_00375](img/DR1_mbne_02500_00375.png)
![DR1_mbne_02500_00625](img/DR1_mbne_02500_00625.png)
![DR1_mbne_02500_01000](img/DR1_mbne_02500_01000.png)
![DR1_mbne_02500_01750](img/DR1_mbne_02500_01750.png)
![DR1_mbne_02500_02500](img/DR1_mbne_02500_02500.png)
![DR1_mbne_03750_00187](img/DR1_mbne_03750_00187.png)
![DR1_mbne_03750_00375](img/DR1_mbne_03750_00375.png)
![DR1_mbne_03750_00562](img/DR1_mbne_03750_00562.png)
![DR1_mbne_03750_00937](img/DR1_mbne_03750_00937.png)
![DR1_mbne_03750_01500](img/DR1_mbne_03750_01500.png)
![DR1_mbne_03750_02625](img/DR1_mbne_03750_02625.png)
![DR1_mbne_03750_03750](img/DR1_mbne_03750_03750.png)
![DR1_mbne_05000_00250](img/DR1_mbne_05000_00250.png)
![DR1_mbne_05000_00500](img/DR1_mbne_05000_00500.png)
![DR1_mbne_05000_00750](img/DR1_mbne_05000_00750.png)
![DR1_mbne_05000_01250](img/DR1_mbne_05000_01250.png)
![DR1_mbne_05000_02000](img/DR1_mbne_05000_02000.png)
![DR1_mbne_05000_03500](img/DR1_mbne_05000_03500.png)
![DR1_mbne_05000_05000](img/DR1_mbne_05000_05000.png)

With wfA data distribution the DR1 method improves AUC slightly,
namely when the training set is small,
with larger training sets the improvement is lower.
And the improvement obtained by softening
(difference of the AUC of softened forest and the AUC
of the same forest before softening)
grows with growing value of the parameter "sampsize".
This difference is also higher with very small forests
(number of trees <= 30).
The same can be said about wfB, but the effect of softening
is generally lower (and often there is no improvement of AUC).

![DR1_wfA_00250_00012](img/DR1_wfA_00250_00012.png)
![DR1_wfA_00250_00025](img/DR1_wfA_00250_00025.png)
![DR1_wfA_00250_00037](img/DR1_wfA_00250_00037.png)
![DR1_wfA_00250_00062](img/DR1_wfA_00250_00062.png)
![DR1_wfA_00250_00100](img/DR1_wfA_00250_00100.png)
![DR1_wfA_00250_00175](img/DR1_wfA_00250_00175.png)
![DR1_wfA_00250_00250](img/DR1_wfA_00250_00250.png)
![DR1_wfA_00500_00025](img/DR1_wfA_00500_00025.png)
![DR1_wfA_00500_00050](img/DR1_wfA_00500_00050.png)
![DR1_wfA_00500_00075](img/DR1_wfA_00500_00075.png)
![DR1_wfA_00500_00125](img/DR1_wfA_00500_00125.png)
![DR1_wfA_00500_00200](img/DR1_wfA_00500_00200.png)
![DR1_wfA_00500_00350](img/DR1_wfA_00500_00350.png)
![DR1_wfA_00500_00500](img/DR1_wfA_00500_00500.png)
![DR1_wfA_01000_00050](img/DR1_wfA_01000_00050.png)
![DR1_wfA_01000_00100](img/DR1_wfA_01000_00100.png)
![DR1_wfA_01000_00150](img/DR1_wfA_01000_00150.png)
![DR1_wfA_01000_00250](img/DR1_wfA_01000_00250.png)
![DR1_wfA_01000_00400](img/DR1_wfA_01000_00400.png)
![DR1_wfA_01000_00700](img/DR1_wfA_01000_00700.png)
![DR1_wfA_01000_01000](img/DR1_wfA_01000_01000.png)
![DR1_wfA_02500_00125](img/DR1_wfA_02500_00125.png)
![DR1_wfA_02500_00250](img/DR1_wfA_02500_00250.png)
![DR1_wfA_02500_00375](img/DR1_wfA_02500_00375.png)
![DR1_wfA_02500_00625](img/DR1_wfA_02500_00625.png)
![DR1_wfA_02500_01000](img/DR1_wfA_02500_01000.png)
![DR1_wfA_02500_01750](img/DR1_wfA_02500_01750.png)
![DR1_wfA_02500_02500](img/DR1_wfA_02500_02500.png)
![DR1_wfA_03750_00187](img/DR1_wfA_03750_00187.png)
![DR1_wfA_03750_00375](img/DR1_wfA_03750_00375.png)
![DR1_wfA_03750_00562](img/DR1_wfA_03750_00562.png)
![DR1_wfA_03750_00937](img/DR1_wfA_03750_00937.png)
![DR1_wfA_03750_01500](img/DR1_wfA_03750_01500.png)
![DR1_wfA_03750_02625](img/DR1_wfA_03750_02625.png)
![DR1_wfA_03750_03750](img/DR1_wfA_03750_03750.png)
![DR1_wfA_05000_00250](img/DR1_wfA_05000_00250.png)
![DR1_wfA_05000_00500](img/DR1_wfA_05000_00500.png)
![DR1_wfA_05000_00750](img/DR1_wfA_05000_00750.png)
![DR1_wfA_05000_01250](img/DR1_wfA_05000_01250.png)
![DR1_wfA_05000_02000](img/DR1_wfA_05000_02000.png)
![DR1_wfA_05000_03500](img/DR1_wfA_05000_03500.png)
![DR1_wfA_05000_05000](img/DR1_wfA_05000_05000.png)

![DR1_wfB_00250_00012](img/DR1_wfB_00250_00012.png)
![DR1_wfB_00250_00025](img/DR1_wfB_00250_00025.png)
![DR1_wfB_00250_00037](img/DR1_wfB_00250_00037.png)
![DR1_wfB_00250_00062](img/DR1_wfB_00250_00062.png)
![DR1_wfB_00250_00100](img/DR1_wfB_00250_00100.png)
![DR1_wfB_00250_00175](img/DR1_wfB_00250_00175.png)
![DR1_wfB_00250_00250](img/DR1_wfB_00250_00250.png)
![DR1_wfB_00500_00025](img/DR1_wfB_00500_00025.png)
![DR1_wfB_00500_00050](img/DR1_wfB_00500_00050.png)
![DR1_wfB_00500_00075](img/DR1_wfB_00500_00075.png)
![DR1_wfB_00500_00125](img/DR1_wfB_00500_00125.png)
![DR1_wfB_00500_00200](img/DR1_wfB_00500_00200.png)
![DR1_wfB_00500_00350](img/DR1_wfB_00500_00350.png)
![DR1_wfB_00500_00500](img/DR1_wfB_00500_00500.png)
![DR1_wfB_01000_00050](img/DR1_wfB_01000_00050.png)
![DR1_wfB_01000_00100](img/DR1_wfB_01000_00100.png)
![DR1_wfB_01000_00150](img/DR1_wfB_01000_00150.png)
![DR1_wfB_01000_00250](img/DR1_wfB_01000_00250.png)
![DR1_wfB_01000_00400](img/DR1_wfB_01000_00400.png)
![DR1_wfB_01000_00700](img/DR1_wfB_01000_00700.png)
![DR1_wfB_01000_01000](img/DR1_wfB_01000_01000.png)
![DR1_wfB_02500_00125](img/DR1_wfB_02500_00125.png)
![DR1_wfB_02500_00250](img/DR1_wfB_02500_00250.png)
![DR1_wfB_02500_00375](img/DR1_wfB_02500_00375.png)
![DR1_wfB_02500_00625](img/DR1_wfB_02500_00625.png)
![DR1_wfB_02500_01000](img/DR1_wfB_02500_01000.png)
![DR1_wfB_02500_01750](img/DR1_wfB_02500_01750.png)
![DR1_wfB_02500_02500](img/DR1_wfB_02500_02500.png)
![DR1_wfB_03750_00187](img/DR1_wfB_03750_00187.png)
![DR1_wfB_03750_00375](img/DR1_wfB_03750_00375.png)
![DR1_wfB_03750_00562](img/DR1_wfB_03750_00562.png)
![DR1_wfB_03750_00937](img/DR1_wfB_03750_00937.png)
![DR1_wfB_03750_01500](img/DR1_wfB_03750_01500.png)
![DR1_wfB_03750_02625](img/DR1_wfB_03750_02625.png)
![DR1_wfB_03750_03750](img/DR1_wfB_03750_03750.png)
![DR1_wfB_05000_00250](img/DR1_wfB_05000_00250.png)
![DR1_wfB_05000_00500](img/DR1_wfB_05000_00500.png)
![DR1_wfB_05000_00750](img/DR1_wfB_05000_00750.png)
![DR1_wfB_05000_01250](img/DR1_wfB_05000_01250.png)
![DR1_wfB_05000_02000](img/DR1_wfB_05000_02000.png)
![DR1_wfB_05000_03500](img/DR1_wfB_05000_03500.png)
![DR1_wfB_05000_05000](img/DR1_wfB_05000_05000.png)


With the EEG Eye State data ("eeges") the positive impact of softening (in the terms of AUC)
can be observed only with training set size 250.
Again, in this case the impact is higher with higher value of "sampsize".
With other (higher) sizes of training set forests softened with the DR1 method
have lower AUC than non-soft forests.

![DR1_eeges_00250_00012](img/DR1_eeges_00250_00012.png)
![DR1_eeges_00250_00025](img/DR1_eeges_00250_00025.png)
![DR1_eeges_00250_00037](img/DR1_eeges_00250_00037.png)
![DR1_eeges_00250_00062](img/DR1_eeges_00250_00062.png)
![DR1_eeges_00250_00100](img/DR1_eeges_00250_00100.png)
![DR1_eeges_00250_00175](img/DR1_eeges_00250_00175.png)
![DR1_eeges_00250_00250](img/DR1_eeges_00250_00250.png)
![DR1_eeges_00499_00024](img/DR1_eeges_00499_00024.png)
![DR1_eeges_00499_00049](img/DR1_eeges_00499_00049.png)
![DR1_eeges_00499_00074](img/DR1_eeges_00499_00074.png)
![DR1_eeges_00499_00124](img/DR1_eeges_00499_00124.png)
![DR1_eeges_00499_00199](img/DR1_eeges_00499_00199.png)
![DR1_eeges_00499_00349](img/DR1_eeges_00499_00349.png)
![DR1_eeges_00499_00499](img/DR1_eeges_00499_00499.png)
![DR1_eeges_00999_00049](img/DR1_eeges_00999_00049.png)
![DR1_eeges_00999_00099](img/DR1_eeges_00999_00099.png)
![DR1_eeges_00999_00149](img/DR1_eeges_00999_00149.png)
![DR1_eeges_00999_00249](img/DR1_eeges_00999_00249.png)
![DR1_eeges_00999_00399](img/DR1_eeges_00999_00399.png)
![DR1_eeges_00999_00699](img/DR1_eeges_00999_00699.png)
![DR1_eeges_00999_00999](img/DR1_eeges_00999_00999.png)
![DR1_eeges_01997_00099](img/DR1_eeges_01997_00099.png)
![DR1_eeges_01997_00199](img/DR1_eeges_01997_00199.png)
![DR1_eeges_01997_00299](img/DR1_eeges_01997_00299.png)
![DR1_eeges_01997_00499](img/DR1_eeges_01997_00499.png)
![DR1_eeges_01997_00798](img/DR1_eeges_01997_00798.png)
![DR1_eeges_01997_01397](img/DR1_eeges_01997_01397.png)
![DR1_eeges_01997_01997](img/DR1_eeges_01997_01997.png)
![DR1_eeges_04994_00249](img/DR1_eeges_04994_00249.png)
![DR1_eeges_04994_00499](img/DR1_eeges_04994_00499.png)
![DR1_eeges_04994_00749](img/DR1_eeges_04994_00749.png)
![DR1_eeges_04994_01248](img/DR1_eeges_04994_01248.png)
![DR1_eeges_04994_01997](img/DR1_eeges_04994_01997.png)
![DR1_eeges_04994_03495](img/DR1_eeges_04994_03495.png)
![DR1_eeges_04994_04994](img/DR1_eeges_04994_04994.png)
![DR1_eeges_07490_00374](img/DR1_eeges_07490_00374.png)
![DR1_eeges_07490_00749](img/DR1_eeges_07490_00749.png)
![DR1_eeges_07490_01123](img/DR1_eeges_07490_01123.png)
![DR1_eeges_07490_01872](img/DR1_eeges_07490_01872.png)
![DR1_eeges_07490_02996](img/DR1_eeges_07490_02996.png)
![DR1_eeges_07490_05243](img/DR1_eeges_07490_05243.png)
![DR1_eeges_07490_07490](img/DR1_eeges_07490_07490.png)
![DR1_eeges_09987_00499](img/DR1_eeges_09987_00499.png)
![DR1_eeges_09987_00998](img/DR1_eeges_09987_00998.png)
![DR1_eeges_09987_01498](img/DR1_eeges_09987_01498.png)
![DR1_eeges_09987_02496](img/DR1_eeges_09987_02496.png)
![DR1_eeges_09987_03994](img/DR1_eeges_09987_03994.png)
![DR1_eeges_09987_06990](img/DR1_eeges_09987_06990.png)
![DR1_eeges_09987_09987](img/DR1_eeges_09987_09987.png)

The Electrical Grid Stability distribution produced forests
which in our experiments were improved by softening if the training set
is smaller than 1333 cases.

![DR1_elgrid_00333_00016](img/DR1_elgrid_00333_00016.png)
![DR1_elgrid_00333_00033](img/DR1_elgrid_00333_00033.png)
![DR1_elgrid_00333_00049](img/DR1_elgrid_00333_00049.png)
![DR1_elgrid_00333_00083](img/DR1_elgrid_00333_00083.png)
![DR1_elgrid_00333_00133](img/DR1_elgrid_00333_00133.png)
![DR1_elgrid_00333_00233](img/DR1_elgrid_00333_00233.png)
![DR1_elgrid_00333_00333](img/DR1_elgrid_00333_00333.png)
![DR1_elgrid_00667_00033](img/DR1_elgrid_00667_00033.png)
![DR1_elgrid_00667_00066](img/DR1_elgrid_00667_00066.png)
![DR1_elgrid_00667_00100](img/DR1_elgrid_00667_00100.png)
![DR1_elgrid_00667_00166](img/DR1_elgrid_00667_00166.png)
![DR1_elgrid_00667_00266](img/DR1_elgrid_00667_00266.png)
![DR1_elgrid_00667_00466](img/DR1_elgrid_00667_00466.png)
![DR1_elgrid_00667_00667](img/DR1_elgrid_00667_00667.png)
![DR1_elgrid_01333_00066](img/DR1_elgrid_01333_00066.png)
![DR1_elgrid_01333_00133](img/DR1_elgrid_01333_00133.png)
![DR1_elgrid_01333_00199](img/DR1_elgrid_01333_00199.png)
![DR1_elgrid_01333_00333](img/DR1_elgrid_01333_00333.png)
![DR1_elgrid_01333_00533](img/DR1_elgrid_01333_00533.png)
![DR1_elgrid_01333_00933](img/DR1_elgrid_01333_00933.png)
![DR1_elgrid_01333_01333](img/DR1_elgrid_01333_01333.png)
![DR1_elgrid_03334_00166](img/DR1_elgrid_03334_00166.png)
![DR1_elgrid_03334_00333](img/DR1_elgrid_03334_00333.png)
![DR1_elgrid_03334_00500](img/DR1_elgrid_03334_00500.png)
![DR1_elgrid_03334_00833](img/DR1_elgrid_03334_00833.png)
![DR1_elgrid_03334_01333](img/DR1_elgrid_03334_01333.png)
![DR1_elgrid_03334_02333](img/DR1_elgrid_03334_02333.png)
![DR1_elgrid_03334_03334](img/DR1_elgrid_03334_03334.png)
![DR1_elgrid_05000_00250](img/DR1_elgrid_05000_00250.png)
![DR1_elgrid_05000_00500](img/DR1_elgrid_05000_00500.png)
![DR1_elgrid_05000_00750](img/DR1_elgrid_05000_00750.png)
![DR1_elgrid_05000_01250](img/DR1_elgrid_05000_01250.png)
![DR1_elgrid_05000_02000](img/DR1_elgrid_05000_02000.png)
![DR1_elgrid_05000_03500](img/DR1_elgrid_05000_03500.png)
![DR1_elgrid_05000_05000](img/DR1_elgrid_05000_05000.png)
![DR1_elgrid_06667_00333](img/DR1_elgrid_06667_00333.png)
![DR1_elgrid_06667_00666](img/DR1_elgrid_06667_00666.png)
![DR1_elgrid_06667_01000](img/DR1_elgrid_06667_01000.png)
![DR1_elgrid_06667_01666](img/DR1_elgrid_06667_01666.png)
![DR1_elgrid_06667_02666](img/DR1_elgrid_06667_02666.png)
![DR1_elgrid_06667_04666](img/DR1_elgrid_06667_04666.png)
![DR1_elgrid_06667_06667](img/DR1_elgrid_06667_06667.png)

With the HTRU2 data set we can see again the strongest positive effect of softening
on the smallest training set.
When the training set size grows, the AUC improvement obtained by softening diminishes
and on the training set of size 11932 there are results from the softened forests
worse than from the original non-soft forests.

![DR1_htru2_00597_00029](img/DR1_htru2_00597_00029.png)
![DR1_htru2_00597_00059](img/DR1_htru2_00597_00059.png)
![DR1_htru2_00597_00089](img/DR1_htru2_00597_00089.png)
![DR1_htru2_00597_00149](img/DR1_htru2_00597_00149.png)
![DR1_htru2_00597_00238](img/DR1_htru2_00597_00238.png)
![DR1_htru2_00597_00417](img/DR1_htru2_00597_00417.png)
![DR1_htru2_00597_00597](img/DR1_htru2_00597_00597.png)
![DR1_htru2_01193_00059](img/DR1_htru2_01193_00059.png)
![DR1_htru2_01193_00119](img/DR1_htru2_01193_00119.png)
![DR1_htru2_01193_00178](img/DR1_htru2_01193_00178.png)
![DR1_htru2_01193_00298](img/DR1_htru2_01193_00298.png)
![DR1_htru2_01193_00477](img/DR1_htru2_01193_00477.png)
![DR1_htru2_01193_00835](img/DR1_htru2_01193_00835.png)
![DR1_htru2_01193_01193](img/DR1_htru2_01193_01193.png)
![DR1_htru2_02386_00119](img/DR1_htru2_02386_00119.png)
![DR1_htru2_02386_00238](img/DR1_htru2_02386_00238.png)
![DR1_htru2_02386_00357](img/DR1_htru2_02386_00357.png)
![DR1_htru2_02386_00596](img/DR1_htru2_02386_00596.png)
![DR1_htru2_02386_00954](img/DR1_htru2_02386_00954.png)
![DR1_htru2_02386_01670](img/DR1_htru2_02386_01670.png)
![DR1_htru2_02386_02386](img/DR1_htru2_02386_02386.png)
![DR1_htru2_05966_00298](img/DR1_htru2_05966_00298.png)
![DR1_htru2_05966_00596](img/DR1_htru2_05966_00596.png)
![DR1_htru2_05966_00894](img/DR1_htru2_05966_00894.png)
![DR1_htru2_05966_01491](img/DR1_htru2_05966_01491.png)
![DR1_htru2_05966_02386](img/DR1_htru2_05966_02386.png)
![DR1_htru2_05966_04176](img/DR1_htru2_05966_04176.png)
![DR1_htru2_05966_05966](img/DR1_htru2_05966_05966.png)
![DR1_htru2_08949_00447](img/DR1_htru2_08949_00447.png)
![DR1_htru2_08949_00894](img/DR1_htru2_08949_00894.png)
![DR1_htru2_08949_01342](img/DR1_htru2_08949_01342.png)
![DR1_htru2_08949_02237](img/DR1_htru2_08949_02237.png)
![DR1_htru2_08949_03579](img/DR1_htru2_08949_03579.png)
![DR1_htru2_08949_06264](img/DR1_htru2_08949_06264.png)
![DR1_htru2_08949_08949](img/DR1_htru2_08949_08949.png)
![DR1_htru2_11932_00596](img/DR1_htru2_11932_00596.png)
![DR1_htru2_11932_01193](img/DR1_htru2_11932_01193.png)
![DR1_htru2_11932_01789](img/DR1_htru2_11932_01789.png)
![DR1_htru2_11932_02983](img/DR1_htru2_11932_02983.png)
![DR1_htru2_11932_04772](img/DR1_htru2_11932_04772.png)
![DR1_htru2_11932_08352](img/DR1_htru2_11932_08352.png)
![DR1_htru2_11932_11932](img/DR1_htru2_11932_11932.png)

The softening method "optim_auc" in our experiments provides results
very similar to the results from the method "DR1".
Although optimization of softening parametes is used in this method
to maximize the AUC on individual trees (evaluated using the training set),
the AUC on test set of the forest is higher than the AUC from the method "DR1"
on very few cases.
These cases fall into two groups:

- The forests where DR1 results are worse than results from the non-soft forest;
  in some of these situations the optim_auc results are better than DR1 results,
  but still worse than non-soft forest.

- The forests with very low number of trees (<=30).

![eeges_00250_00012](img/eeges_00250_00012.png)
![eeges_00250_00025](img/eeges_00250_00025.png)
![eeges_00250_00037](img/eeges_00250_00037.png)
![eeges_00250_00062](img/eeges_00250_00062.png)
![eeges_00250_00100](img/eeges_00250_00100.png)
![eeges_00250_00175](img/eeges_00250_00175.png)
![eeges_00250_00250](img/eeges_00250_00250.png)
![eeges_00499_00024](img/eeges_00499_00024.png)
![eeges_00499_00049](img/eeges_00499_00049.png)
![eeges_00499_00074](img/eeges_00499_00074.png)
![eeges_00499_00124](img/eeges_00499_00124.png)
![eeges_00499_00199](img/eeges_00499_00199.png)
![eeges_00499_00349](img/eeges_00499_00349.png)
![eeges_00499_00499](img/eeges_00499_00499.png)
![eeges_00999_00049](img/eeges_00999_00049.png)
![eeges_00999_00099](img/eeges_00999_00099.png)
![eeges_00999_00149](img/eeges_00999_00149.png)
![eeges_00999_00249](img/eeges_00999_00249.png)
![eeges_00999_00399](img/eeges_00999_00399.png)
![eeges_00999_00699](img/eeges_00999_00699.png)
![eeges_00999_00999](img/eeges_00999_00999.png)
![eeges_01997_00099](img/eeges_01997_00099.png)
![eeges_01997_00199](img/eeges_01997_00199.png)
![eeges_01997_00299](img/eeges_01997_00299.png)
![eeges_01997_00499](img/eeges_01997_00499.png)
![eeges_01997_00798](img/eeges_01997_00798.png)
![eeges_01997_01397](img/eeges_01997_01397.png)
![eeges_01997_01997](img/eeges_01997_01997.png)
---
![elgrid_00333_00016](img/elgrid_00333_00016.png)
![elgrid_00333_00033](img/elgrid_00333_00033.png)
![elgrid_00333_00049](img/elgrid_00333_00049.png)
![elgrid_00333_00083](img/elgrid_00333_00083.png)
![elgrid_00333_00133](img/elgrid_00333_00133.png)
![elgrid_00333_00233](img/elgrid_00333_00233.png)
![elgrid_00333_00333](img/elgrid_00333_00333.png)
![elgrid_00667_00033](img/elgrid_00667_00033.png)
![elgrid_00667_00066](img/elgrid_00667_00066.png)
![elgrid_00667_00100](img/elgrid_00667_00100.png)
![elgrid_00667_00166](img/elgrid_00667_00166.png)
![elgrid_00667_00266](img/elgrid_00667_00266.png)
![elgrid_00667_00466](img/elgrid_00667_00466.png)
![elgrid_00667_00667](img/elgrid_00667_00667.png)
![elgrid_01333_00066](img/elgrid_01333_00066.png)
![elgrid_01333_00133](img/elgrid_01333_00133.png)
![elgrid_01333_00199](img/elgrid_01333_00199.png)
![elgrid_01333_00333](img/elgrid_01333_00333.png)
![elgrid_01333_00533](img/elgrid_01333_00533.png)
![elgrid_01333_00933](img/elgrid_01333_00933.png)
![elgrid_01333_01333](img/elgrid_01333_01333.png)
------
![htru2_00597_00029](img/htru2_00597_00029.png)
![htru2_00597_00059](img/htru2_00597_00059.png)
![htru2_00597_00089](img/htru2_00597_00089.png)
![htru2_00597_00149](img/htru2_00597_00149.png)
![htru2_00597_00238](img/htru2_00597_00238.png)
![htru2_00597_00417](img/htru2_00597_00417.png)
![htru2_00597_00597](img/htru2_00597_00597.png)
![htru2_01193_00059](img/htru2_01193_00059.png)
![htru2_01193_00119](img/htru2_01193_00119.png)
![htru2_01193_00178](img/htru2_01193_00178.png)
![htru2_01193_00298](img/htru2_01193_00298.png)
![htru2_01193_00477](img/htru2_01193_00477.png)
![htru2_01193_00835](img/htru2_01193_00835.png)
![htru2_01193_01193](img/htru2_01193_01193.png)
![htru2_02386_00119](img/htru2_02386_00119.png)
![htru2_02386_00238](img/htru2_02386_00238.png)
![htru2_02386_00357](img/htru2_02386_00357.png)
![htru2_02386_00596](img/htru2_02386_00596.png)
![htru2_02386_00954](img/htru2_02386_00954.png)
![htru2_02386_01670](img/htru2_02386_01670.png)
![htru2_02386_02386](img/htru2_02386_02386.png)
![htru2_05966_00298](img/htru2_05966_00298.png)
![htru2_05966_00596](img/htru2_05966_00596.png)
![htru2_05966_00894](img/htru2_05966_00894.png)
![htru2_05966_01491](img/htru2_05966_01491.png)
![htru2_05966_02386](img/htru2_05966_02386.png)
![htru2_05966_04176](img/htru2_05966_04176.png)
![htru2_05966_05966](img/htru2_05966_05966.png)
-------
![magic_00250_00012](img/magic_00250_00012.png)
![magic_00250_00025](img/magic_00250_00025.png)
![magic_00250_00037](img/magic_00250_00037.png)
![magic_00250_00062](img/magic_00250_00062.png)
![magic_00250_00100](img/magic_00250_00100.png)
![magic_00250_00175](img/magic_00250_00175.png)
![magic_00250_00250](img/magic_00250_00250.png)
![magic_00500_00025](img/magic_00500_00025.png)
![magic_00500_00050](img/magic_00500_00050.png)
![magic_00500_00075](img/magic_00500_00075.png)
![magic_00500_00125](img/magic_00500_00125.png)
![magic_00500_00200](img/magic_00500_00200.png)
![magic_00500_00350](img/magic_00500_00350.png)
![magic_00500_00500](img/magic_00500_00500.png)
![magic_01000_00050](img/magic_01000_00050.png)
![magic_01000_00100](img/magic_01000_00100.png)
![magic_01000_00150](img/magic_01000_00150.png)
![magic_01000_00250](img/magic_01000_00250.png)
![magic_01000_00400](img/magic_01000_00400.png)
![magic_01000_00700](img/magic_01000_00700.png)
![magic_01000_01000](img/magic_01000_01000.png)
![magic_02500_00125](img/magic_02500_00125.png)
![magic_02500_00250](img/magic_02500_00250.png)
![magic_02500_00375](img/magic_02500_00375.png)
![magic_02500_00625](img/magic_02500_00625.png)
![magic_02500_01000](img/magic_02500_01000.png)
![magic_02500_01750](img/magic_02500_01750.png)
![magic_02500_02500](img/magic_02500_02500.png)
------
![mbne_00250_00012](img/mbne_00250_00012.png)
![mbne_00250_00025](img/mbne_00250_00025.png)
![mbne_00250_00037](img/mbne_00250_00037.png)
![mbne_00250_00062](img/mbne_00250_00062.png)
![mbne_00250_00100](img/mbne_00250_00100.png)
![mbne_00250_00175](img/mbne_00250_00175.png)
![mbne_00250_00250](img/mbne_00250_00250.png)
![mbne_00500_00025](img/mbne_00500_00025.png)
![mbne_00500_00050](img/mbne_00500_00050.png)
![mbne_00500_00075](img/mbne_00500_00075.png)
![mbne_00500_00125](img/mbne_00500_00125.png)
![mbne_00500_00200](img/mbne_00500_00200.png)
![mbne_00500_00350](img/mbne_00500_00350.png)
![mbne_00500_00500](img/mbne_00500_00500.png)
![mbne_01000_00050](img/mbne_01000_00050.png)
![mbne_01000_00100](img/mbne_01000_00100.png)
![mbne_01000_00150](img/mbne_01000_00150.png)
![mbne_01000_00250](img/mbne_01000_00250.png)
![mbne_01000_00400](img/mbne_01000_00400.png)
![mbne_01000_00700](img/mbne_01000_00700.png)
![mbne_01000_01000](img/mbne_01000_01000.png)
![mbne_02500_00125](img/mbne_02500_00125.png)
![mbne_02500_00250](img/mbne_02500_00250.png)
![mbne_02500_00375](img/mbne_02500_00375.png)
![mbne_02500_00625](img/mbne_02500_00625.png)
![mbne_02500_01000](img/mbne_02500_01000.png)
![mbne_02500_01750](img/mbne_02500_01750.png)
![mbne_02500_02500](img/mbne_02500_02500.png)
-------
![wfA_00250_00012](img/wfA_00250_00012.png)
![wfA_00250_00025](img/wfA_00250_00025.png)
![wfA_00250_00037](img/wfA_00250_00037.png)
![wfA_00250_00062](img/wfA_00250_00062.png)
![wfA_00250_00100](img/wfA_00250_00100.png)
![wfA_00250_00175](img/wfA_00250_00175.png)
![wfA_00250_00250](img/wfA_00250_00250.png)
![wfA_00500_00025](img/wfA_00500_00025.png)
![wfA_00500_00050](img/wfA_00500_00050.png)
![wfA_00500_00075](img/wfA_00500_00075.png)
![wfA_00500_00125](img/wfA_00500_00125.png)
![wfA_00500_00200](img/wfA_00500_00200.png)
![wfA_00500_00350](img/wfA_00500_00350.png)
![wfA_00500_00500](img/wfA_00500_00500.png)
![wfA_01000_00050](img/wfA_01000_00050.png)
![wfA_01000_00100](img/wfA_01000_00100.png)
![wfA_01000_00150](img/wfA_01000_00150.png)
![wfA_01000_00250](img/wfA_01000_00250.png)
![wfA_01000_00400](img/wfA_01000_00400.png)
![wfA_01000_00700](img/wfA_01000_00700.png)
![wfA_01000_01000](img/wfA_01000_01000.png)
![wfA_02500_00125](img/wfA_02500_00125.png)
![wfA_02500_00250](img/wfA_02500_00250.png)
![wfA_02500_00375](img/wfA_02500_00375.png)
![wfA_02500_00625](img/wfA_02500_00625.png)
![wfA_02500_01000](img/wfA_02500_01000.png)
![wfA_02500_01750](img/wfA_02500_01750.png)
![wfA_02500_02500](img/wfA_02500_02500.png)
-------
![wfB_00250_00012](img/wfB_00250_00012.png)
![wfB_00250_00025](img/wfB_00250_00025.png)
![wfB_00250_00037](img/wfB_00250_00037.png)
![wfB_00250_00062](img/wfB_00250_00062.png)
![wfB_00250_00100](img/wfB_00250_00100.png)
![wfB_00250_00175](img/wfB_00250_00175.png)
![wfB_00250_00250](img/wfB_00250_00250.png)
![wfB_00500_00025](img/wfB_00500_00025.png)
![wfB_00500_00050](img/wfB_00500_00050.png)
![wfB_00500_00075](img/wfB_00500_00075.png)
![wfB_00500_00125](img/wfB_00500_00125.png)
![wfB_00500_00200](img/wfB_00500_00200.png)
![wfB_00500_00350](img/wfB_00500_00350.png)
![wfB_00500_00500](img/wfB_00500_00500.png)
![wfB_01000_00050](img/wfB_01000_00050.png)
![wfB_01000_00100](img/wfB_01000_00100.png)
![wfB_01000_00150](img/wfB_01000_00150.png)
![wfB_01000_00250](img/wfB_01000_00250.png)
![wfB_01000_00400](img/wfB_01000_00400.png)
![wfB_01000_00700](img/wfB_01000_00700.png)
![wfB_01000_01000](img/wfB_01000_01000.png)
![wfB_02500_00125](img/wfB_02500_00125.png)
![wfB_02500_00250](img/wfB_02500_00250.png)
![wfB_02500_00375](img/wfB_02500_00375.png)
![wfB_02500_00625](img/wfB_02500_00625.png)
![wfB_02500_01000](img/wfB_02500_01000.png)
![wfB_02500_01750](img/wfB_02500_01750.png)
![wfB_02500_02500](img/wfB_02500_02500.png)


## Conclusion

When softening methods were used to trees in random forests and results
were compared by AUC, a positive effect of softening was observed in situations
where a training set was small.

In some cases the results of forests with softened trees were worse than results
of the original non-soft forests.

More sofisticated softening method optim_auc
seems to exceed both the simple method DR1 and no softening
only with very small forests.


## References

[1]: Dvořák, Jakub: *Classification trees with soft splits optimized for ranking*.
Computational Statistics (2019) 34:763–786.

[2]: Breiman, Leo: *Random Forests*. Machine Learning (2001) 1:5-32.

