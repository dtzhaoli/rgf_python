[![Build Status](https://travis-ci.org/fukatani/rgf_python.svg?branch=master)](https://travis-ci.org/fukatani/rgf_python)
[![License](https://img.shields.io/github/license/fukatani/rgf_python.svg)](https://github.com/fukatani/rgf_python/blob/master/LICENSE)

# rgf_python
The wrapper of machine learning algorithm ***Regularized Greedy Forest (RGF)*** for Python.

## Features

##### Scikit-learn interface and possibility of usage for multi-label classification problem.

Original RGF implementation is available only for regression and binary classification, but rgf_python is **also available for multi-label classification** by "One-vs-Rest" method.

Example:
```python
from sklearn import datasets
from sklearn.utils.validation import check_random_state
from sklearn.cross_validation import StratifiedKFold
from rgf.rgf import RGFClassifier

iris = datasets.load_iris()
rng = check_random_state(0)
perm = rng.permutation(iris.target.size)
iris.data = iris.data[perm]
iris.target = iris.target[perm]

rgf = RGFClassifier(max_leaf=400,
                    algorithm="RGF_Sib",
                    test_interval=100,)

# cross validation
rgf_score = 0
n_folds = 3

for train_idx, test_idx in StratifiedKFold(iris.target, n_folds):
    xs_train = iris.data[train_idx]
    y_train = iris.target[train_idx]
    xs_test = iris.data[test_idx]
    y_test = iris.target[test_idx]

    rgf.fit(xs_train, y_train)
    rgf_score += rgf.score(xs_test, y_test)

rgf_score /= n_folds
print('score: {0}'.format(rgf_score))
```

At the moment, rgf_python works only in single thread mode, so if you use GridSearchCV you should set the `n_jobs` parameter to 1.

## Software Requirements

* Python (2.7 or 3.4)
* scikit-learn (0.18 or later)
* RGF C++ ([link](http://tongzhang-ml.org/software/rgf/index.html))

If you can't access the above URL, alternatively, you can get RGF C++ by downloading it from this [page](https://github.com/fukatani/rgf_python/releases/download/0.2.0/rgf1.2.zip).
Please see README in the zip file to build RGF executional.


## Installation

```
git clone https://github.com/fukatani/rgf_python.git
python setup.py install
```
or using `pip`:
```
pip install git+git://github.com/fukatani/rgf_python@master
```

You have to place RGF execution file in directory which is included in **environmental variable 'PATH'.**
Or you can direct specify path by **manual editing rgf/rgf.py**

```python
## Edit this ##################################################

#Location of the RGF executable
loc_exec = 'C:\\Users\\rf\\Documents\\python\\rgf1.2\\bin\\rgf.exe'
#Location for RGF temp files
#Must not include whitespace characters
loc_temp = 'temp/'

## End Edit ##################################################
```

You need to set actual location of RGF execution file by editing 'loc_exec'.
And the variable 'loc_temp' can be changed to specify the directory for placing temp files.

## Tuning the Hyper-parameters
You can tune hyper-parameters as follows.

* _max_leaf_: Appropriate values are data-dependent and usually varied from 1000 to 10000.

* _test_interval_: For efficiency, it must be either multiple or divisor of 100 (default value of the optimization interval).

* _algorithm_: You can select "RGF", "RGF Opt" or "RGF Sib".

* _loss_: You can select "LS", "Log" or "Expo".

* _reg_depth_: Must be no smaller than 1. Meant for being used with _algorithm_ = "RGF Opt" or "RGF Sib".

* _l2_: Either 1, 0.1, or 0.01 often produces good results though with exponential loss (_loss_ = "Expo") and logistic loss (_loss_ = "Log"), some data requires smaller values such as 1e-10 or 1e-20.

* _sl2_: Default value is equal to _l2_. On some data, _l2_/100 works well.

Detailed instruction of tuning hyper-parameters is [here](http://tongzhang-ml.org/software/rgf/rgf1.2-guide.pdf).

## Using at Kaggle Kernel
Now, Kaggle Kernel supports rgf_python. Please see [this page](https://www.kaggle.com/fukatani/d/uciml/iris/classification-by-regularized-greedy-forest).

## Other
Shamelessly, many part of the implementation is based on the following [code](https://github.com/MLWave/RGF-sklearn). Thanks!