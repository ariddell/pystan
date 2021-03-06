PyStan: The Python Interface to Stan
====================================

.. image:: https://raw.githubusercontent.com/stan-dev/logos/master/logo.png
    :alt: Stan logo
    :scale: 50 %

|pypi| |travis| |appveyor| |zenodo|

.. tip:: PyStan 3 is available for Linux and macOS users. Visit the `PyStan 3 documentation <https://pystan.readthedocs.io/en/latest/>`_ for details. PyStan 2 is not maintained.

**PyStan** provides a Python interface to Stan, a package for Bayesian inference
using the No-U-Turn sampler, a variant of Hamiltonian Monte Carlo.

For more information on `Stan <http://mc-stan.org>`_ and its modeling language,
see the Stan User's Guide and Reference Manual at `http://mc-stan.org/
<http://mc-stan.org/>`_.


Important links
---------------

- HTML documentation: https://pystan2.readthedocs.org
- Issue tracker: https://github.com/stan-dev/pystan/issues
- Source code repository: https://github.com/stan-dev/pystan
- Stan: http://mc-stan.org/
- Stan User's Guide and Reference Manual (pdf) available at http://mc-stan.org

Related projects
----------------

- ArviZ: `Exploratory analysis of Bayesian models with Python <https://github.com/arviz-devs/arviz>`_ by @arviz-devs
- Jupyter tool: `StanMagic <https://github.com/Arvinds-ds/stanmagic>`_ by @Arvinds-ds
- Jupyter tool: `JupyterStan <https://github.com/janfreyberg/jupyterstan>`_ by @janfreyberg
- Scikit-learn integration: `pystan-sklearn <https://github.com/rgerkin/pystan-sklearn>`_ by @rgerkin.

Projects using PyStan
---------------------
- BAMBI: `BAyesian Model-Building Interface <https://github.com/bambinos/bambi>`_ by @bambinos
- hBayesDM: `hierarchical Bayesian modeling of Decision-Making tasks <https://hbayesdm.readthedocs.io>`_ by @CCS-Lab
- Orbit: `Object-oRiented BayesIan Timeseries models <https://github.com/uber/orbit>`_ by @uber
- Prophet: `Timeseries forecasting <https://facebook.github.io/prophet/>`_ by @facebook

Similar projects
----------------

- PyMC3: https://docs.pymc.io/
- emcee: https://emcee.readthedocs.io/en/stable/

PyStan3 / Stan3
---------------
The development of PyStan3 with updated API can be found under `stan-dev/pystan-next <https://github.com/stan-dev/pystan-next>`_

Detailed Installation Instructions
----------------------------------
Detailed installation instructions can be found in the
`doc/installation_beginner.md <doc/installation_beginner.rst/>`_ file.

Windows Installation Instructions
---------------------------------
Detailed installation instructions for Windows can be found in docs under `PyStan on Windows <https://pystan2.readthedocs.io/en/latest/windows.html>`_

Quick Installation (Linux and macOS)
------------------------------------

`NumPy  <http://www.numpy.org/>`_ and `Cython <http://www.cython.org/>`_
(version 0.22 or greater) are required. `matplotlib <http://matplotlib.org/>`_
is optional. ArviZ is recommended for visualization and analysis.

PyStan and the required packages may be installed from the `Python Package Index
<https://pypi.python.org/pypi>`_ using ``pip``.

::

   pip install pystan

Alternatively, if Cython (version 0.22 or greater) and NumPy are already
available, PyStan may be installed from source with the following commands

::

   git clone --recursive https://github.com/stan-dev/pystan.git
   cd pystan
   python setup.py install

To install latest development version user can also use ``pip``

::

    pip install git+https://github.com/stan-dev/pystan

If you encounter an ``ImportError`` after compiling from source, try changing
out of the source directory before attempting ``import pystan``. On Linux and
OS X ``cd /tmp`` will work.

``make`` (``mingw32-make`` on Windows) is a requirement for building from source.

Example
-------

.. code-block:: python

    import pystan
    import numpy as np
    import matplotlib.pyplot as plt

    schools_code = """
    data {
        int<lower=0> J; // number of schools
        real y[J]; // estimated treatment effects
        real<lower=0> sigma[J]; // s.e. of effect estimates
    }
    parameters {
        real mu;
        real<lower=0> tau;
        real eta[J];
    }
    transformed parameters {
        real theta[J];
        for (j in 1:J)
            theta[j] = mu + tau * eta[j];
    }
    model {
        eta ~ normal(0, 1);
        y ~ normal(theta, sigma);
    }
    """

    schools_dat = {'J': 8,
                   'y': [28,  8, -3,  7, -1,  1, 18, 12],
                   'sigma': [15, 10, 16, 11,  9, 11, 10, 18]}

    sm = pystan.StanModel(model_code=schools_code)
    fit = sm.sampling(data=schools_dat, iter=1000, chains=4)

    print(fit)

    eta = fit.extract(permuted=True)['eta']
    np.mean(eta, axis=0)

    # if matplotlib is installed (optional, not required), a visual summary and
    # traceplot are available
    fit.plot()
    plt.show()

    # updated traceplot can be plotted with
    import arviz as az
    az.plot_trace(fit)

.. |pypi| image:: https://badge.fury.io/py/pystan.png
    :target: https://badge.fury.io/py/pystan
    :alt: pypi version

.. |travis| image:: https://travis-ci.org/stan-dev/pystan.png?branch=master
    :target: https://travis-ci.org/stan-dev/pystan
    :alt: travis-ci build status

.. |appveyor| image:: https://ci.appveyor.com/api/projects/status/49e69yl5ngxkpmab?svg=true
    :target: https://ci.appveyor.com/project/pystan/pystan
    :alt: appveyor-ci build status

.. |zenodo| image:: https://zenodo.org/badge/10256919.svg
    :target: https://zenodo.org/badge/latestdoi/10256919
    :alt: zenodo citation DOI
