# GMM-MI 

Welcome to GMM-MI (pronounced ``Jimmie``)! This package allows you to calculate mutual information (MI) with its associated uncertainty, combining Gaussian mixture models (GMMs) and bootstrap. GMM-MI is computationally efficient and fully in python. You can read more about GMM-MI [in our paper](https://www.overleaf.com/project/62920145c884448df7e9745c) (the link will be to the actual paper once submitted). Please [cite it](#citation) if you use it in your work!

Current missing features include:
- can we make it faster with some form of parallelisation? especially CV!
- reflect recent code changes in the paper draft

## Installation

To install GMM-MI, we currently recommend the following steps:
1. (optional) `conda create -n gmm_mi python=3.7 jupyter` (we recommend creating a custom `conda` environment) 
2. (optional) `conda activate gmm_mi` (activate it)
3. `git clone https://github.com/dpiras/MI_estimation.git` (clone repository; with `https` you need to insert your GH credentials)
4. `cd MI_estimation` (move into cloned folder)
5. `python setup.py install` (install `gmm_mi` and all its dependencies); alternatively, `pip install .` should also work.
6. `pytest` (to make sure the installation worked correctly)

We will make the package `pip` installable once we make the repository public, and update these instructions.

## Usage

To use GMM-MI, you simply need to import the class EstimateMI, choose the hyperparameters and fit it to your data. You can find an example below. A description of the hyperparameters that you can play with can be found [here](https://github.com/dpiras/MI_estimation/blob/main/gmm_mi/mi.py#L6), and we discuss a few of them [below](#hyperparameter-description).

## Example

Once you installed GMM-MI, calculating the distribution of mutual information on your data is as easy as:

    import numpy as np
    from gmm_mi.mi import EstimateMI
    # create simple bivariate Gaussian data
    mean, cov = np.array([0, 0]), np.array([[1, 0.6], [0.6, 1]])
    rng = np.random.default_rng(0)
    X = rng.multivariate_normal(mean, cov, 200) # has shape (200, 2)
    # calculate MI
    MIEstimator = EstimateMI()
    MI_mean, MI_std = MIEstimator.fit(X)

This yields (0.21 &pm; 0.04) nats, well in agreement with the theoretical value of 0.22 nats. If you want to visualize the fitted model over your input data, you can run:
    
    MIEstimator.plot_fitted_model()

More example notebooks, including all results from the paper, are available in [`notebooks`](https://github.com/dpiras/MI_estimation/blob/main/notebooks).

## Hyperparameter description

    n_folds : int, default=2
        Number of folds in the cross-validation (CV) performed to find the best initialization parameters.
        As in every CV procedure, there is no best value. A good value, though, should ensure each fold has
        enough samples to be representative of your training set.

    n_inits : int, default=3
        Number of initializations used to find the best initialization parameters.
        Higher values will decrease the chances of stopping at a local optimum, while making the code slower.	
    
    reg_covar : float, default=1e-15
        The constant term added to the diagonal of the covariance matrices to avoid singularities.
        Smaller values will increase the chances of singular matrices, but will have a smaller impact
        on the final MI estimates.
    threshold_fit : float, default=1e-5
        The log-likelihood threshold on each GMM fit used to choose when to stop training.
        Smaller values will improve the fit quality and reduce the chances of stopping at a local optimum,
        while making the code considerably slower. This is equivalent to `tol` in sklearn GMMs.       
    threshold_components : float, default=1e-5
        The metric threshold to decide when to stop adding GMM components. In other words, GMM-MI stops 
        adding components either when the metric gets worse, or when the improvement in the metric value
        is less than this threshold.
        Smaller values ensure that enough components are considered and thus that the data distribution is 
        correctly captured, while taking longer to converge and possibly insignificantly changing the 
        final value of MI.
    patience : int, default=1, 
        Number of extra components to "wait" until convergence is declared. Must be at least 1.
        Same concept as patience when training a neural network. Higher value will fit models
        with higher numbers of GMM components, while taking longer to converge.
    n_bootstrap : int, default=50 
        Number of bootstrap realisations to consider to obtain the MI uncertainty.
        Higher values will return a better estimate of the MI uncertainty, and
        will make the MI distribution more Gaussian-like, but the code will take longer.
    MC_samples : int, default=1e5
        Number of MC samples to use to estimate the MI integral. Only used if MI_method == 'MC'.
        Higher values will return less noisy estimates of MI, but will take longer.

## Contributing and contacts

Feel free to [fork](https://github.com/dpiras/MI_estimation/fork) this repository to work on it; otherwise, please [raise an issue](https://github.com/dpiras/MI_estimation/issues) or contact [Davide Piras](mailto:dr.davide.piras@gmail.com).

## Citation
If you use GMM-MI, please cite the corresponding paper:

     @article{TBC, 
        author = {TBC},
         title = {TBC},
       journal = {TBC},
        eprint = {TBC},
          year = {TBC}
     }

## License

GMM-MI is released under the GPL-3 license - see [LICENSE](https://github.com/dpiras/MI_estimation/blob/main/LICENSE.txt)-, subject to 
the non-commercial use condition - see [LICENSE_EXT](https://github.com/dpiras/MI_estimation/blob/main/LICENSE_EXT.txt).

     GMM-MI
     Copyright (C) 2022 Davide Piras & contributors

     This program is released under the GPL-3 license (see LICENSE.txt), 
     subject to a non-commercial use condition (see LICENSE_EXT.txt).

     This program is distributed in the hope that it will be useful,
     but WITHOUT ANY WARRANTY; without even the implied warranty of
     MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
