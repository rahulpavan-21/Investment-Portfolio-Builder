# Investment-Portfolio-Builder

## Overview

Investment Portfolio Builder is a Python library that provides portfolio optimization methods, including classical mean-variance optimization techniques, Black-Litterman allocation, and more recent developments like shrinkage and Hierarchical Risk Parity. It is both extensive and easily extensible, making it useful for casual investors and professionals looking for a prototyping tool. Whether you are a fundamentals-oriented investor or an algorithmic trader, Investment Portfolio Builder can help you combine your alpha sources in a risk-efficient way.

## Getting started

Instructions:

_Note: macOS users will need to install [Command Line Tools](https://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/)._

_Note: if you are on windows, you first need to installl C++. ([download](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16), [install instructions](https://drive.google.com/file/d/0B4GsMXCRaSSIOWpYQkstajlYZ0tPVkNQSElmTWh1dXFaYkJr/view))_

This project is available on PyPI, meaning that you can just:

```bash
pip install PyPortfolioOpt
```

(you may need to follow separate installation instructions for [cvxopt](https://cvxopt.org/install/index.html#) and [cvxpy](https://www.cvxpy.org/install/)).

However, it is best practice to use a dependency manager within a virtual environment.
My current recommendation is to get yourself set up with [poetry](https://github.com/sdispater/poetry) then just run

```bash
poetry add PyPortfolioOpt
```

Otherwise, clone/download the project and in the project directory run:

```bash
python setup.py install
```

PyPortfolioOpt supports Docker. Build your first container with `docker build -f docker/Dockerfile . -t pypfopt`. You can use the image to run tests or even launch a Jupyter server.

```bash
# iPython interpreter:
docker run -it pypfopt poetry run ipython

# Jupyter notebook server:
docker run -it -p 8888:8888 pypfopt poetry run jupyter notebook --allow-root --no-browser --ip 0.0.0.0
# click on http://127.0.0.1:8888/?token=xxx

# Pytest
docker run -t pypfopt poetry run pytest

# Bash
docker run -it pypfopt bash
```

For more information, please read [this guide](https://docker-curriculum.com/#introduction).



## A quick example

Here is an example on real life stock data, demonstrating how easy it is to find the long-only portfolio that maximises the Sharpe ratio (a measure of risk-adjusted returns).

```python
import pandas as pd
from pypfopt import EfficientFrontier
from pypfopt import risk_models
from pypfopt import expected_returns

# Read in price data
df = pd.read_csv("tests/resources/stock_prices.csv", parse_dates=True, index_col="date")

# Calculate expected returns and sample covariance
mu = expected_returns.mean_historical_return(df)
S = risk_models.sample_cov(df)

# Optimize for maximal Sharpe ratio
ef = EfficientFrontier(mu, S)
raw_weights = ef.max_sharpe()
cleaned_weights = ef.clean_weights()
ef.save_weights_to_file("weights.csv")  # saves to file
print(cleaned_weights)
ef.portfolio_performance(verbose=True)
```

This outputs the following weights:

```txt
{'GOOG': 0.03835,
 'AAPL': 0.0689,
 'FB': 0.20603,
 'BABA': 0.07315,
 'AMZN': 0.04033,
 'GE': 0.0,
 'AMD': 0.0,
 'WMT': 0.0,
 'BAC': 0.0,
 'GM': 0.0,
 'T': 0.0,
 'UAA': 0.0,
 'SHLD': 0.0,
 'XOM': 0.0,
 'RRC': 0.0,
 'BBY': 0.01324,
 'MA': 0.35349,
 'PFE': 0.1957,
 'JPM': 0.0,
 'SBUX': 0.01082}

Expected annual return: 30.5%
Annual volatility: 22.2%
Sharpe Ratio: 1.28
```





### Expected returns

-   Mean historical returns:
    -   the simplest and most common approach, which states that the expected return of each asset is equal to the mean of its historical returns.
    -   easily interpretable and very intuitive
-   Exponentially weighted mean historical returns:
    -   similar to mean historical returns, except it gives exponentially more weight to recent prices
    -   it is likely the case that an asset's most recent returns hold more weight than returns from 10 years ago when it comes to estimating future returns.
-   Capital Asset Pricing Model (CAPM):
    -   a simple model to predict returns based on the beta to the market
    -   this is used all over finance!





