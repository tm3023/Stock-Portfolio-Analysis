# Stock Portfolio Analysis 
Mathematical methods are used to build a diverse stock portfolio from S&amp;P 500 stocks. Monte Carlo simulations are then used to estimate the distribution of future returns and risk metrics of the portfolio before and after weighting optimisation. An efficient frontier is used to find weightings that meet desired performance criteria. These methods are beneficial for determining an optimal investment strategy and mitigating risk.

## Stock Selection for Portfolio
In the following we will build a portfolio of S&P 500 stocks and use Python's ```yfinance``` module to obtain historical data of the selected stocks. We may build a diverse portfolio manually with stocks an investor is already interested in, however we have automated this process by extracting a random sample of tickers from a table of S&P 500 stocks extracted from Wikipedia and then using k-means clustering to sort the stocks by specific attributes. This allows for a more analytical approach to diversification as opposed to diversifying by industry. We must decide on the number of clusters to assign the stocks to, so we may use inertia as a measure of how well-fitting the clusters are. Inertia decreases as k increases; the elbow method identifies the point at which additional clusters yield diminishing improvement.
<Figure size 800x500 with 1 Axes><img width="686" height="470" alt="image" src="https://github.com/user-attachments/assets/2c34f969-5cf2-4f20-9cb4-a56cc10dd6c7" />

Optimal number of clusters: 4

The elbow method is then used to determine the optimal value for k, past which values give diminishing returns of how well the stocks fit into the clusters. 
The top perfoming stocks by Sharpe ratio are then picked out from each cluster. In this case we have the following:

Selected Securities from Clusters:

['CVS', 'ARES', 'LII', 'KIM', 'AXON', 'UAL', 'TTD']

## Monte Carlo Simulatons
Now we have our portfolio of stocks, we carry out Monte Carlo simulations to analyse the performance of the portfolio based on specified weightings. First we will apply a uniform weighting of the stocks in our portfolio where price paths of our portfolio are modelled using the cumulative product of returns under a multivariate normal distribution $\mathcal{MVN}(\mu, \Sigma)$, where $\mu$ is a column vector such that each entry contains the historical mean returns of a stock and $\Sigma$ is the corresponding covariance matrix for the returns. Here we have used aggregate historical data over a preiod of one year. The following shows the price paths of 1000 simulated portfolios over a period of 100 days:

<img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/fc71383f-1dab-4a79-bea6-493d036d4655" />

Using the ending values of the simulated portfolios, a histogram plot can be used to compute useful performance metrics.

<img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/331f9250-e345-4352-9f90-422491a4d077" />

Monte Carlo Portfolio Performance

Mean Return: 4.63%

Std Dev of Return: 16.88%

Value at Risk (5%): 19.73%

Conditional VaR (5%): 25.60%

Mean Max Drawdown: -14.87%

Note: Here the VaR represents the percentage loss of the original portfolio value.

Importantly, we would like to optimise the performance of the portfolio in a specified way. Here we will achieve this by optimising the weighting of the stocks in the portfolio to maximise the Sharpe ratio. By defining the negative Sharpe ratio of a portfolio along with relevant bounds and constraints (i.e. each weight is a value between 0 and 1 where the sum off all weights is 1), ```scipy.optimize.minimize()``` returns our desired weighting:

Optimized Weights (Max Sharpe):

CVS: 0.00%

ARES: 0.00%

LII: 0.00%

KIM: 2.15%

AXON: 61.75%

UAL: 36.10%

TTD: 0.00%

We see that the optimised Max Sharpe weights concentrate heavily in AXON (61.75%) and UAL (36.10%) — this is a well-known problem with unconstrained MVO called weight concentration: thic could be adressed by maximum weight constraints or regularisation.
Then we can run Monte Carlo simulations using this weighting, keeping the number of simulations and time period constant:

<img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/ab24559b-aea9-43c8-b29b-ad86d04875e1" />

<img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/f63704a0-4605-4fd3-8dfb-6c2b694ace8c" />

Monte Carlo Portfolio Performance:

Mean Return: 22.48%

Std Dev of Return: 33.30%

Value at Risk (5%): 23.26%

Conditional VaR (5%): 30.26%

Mean Max Drawdown: -21.01%

Here we see we have improved the expected retun of the portfolio at the cost of increased risk. We could optimise our weighting under different criteria to potentially mitigate this.

## Efficient Frontier
We may also utilise an efficient frontier to find weightings that meet other perfomance criteria such as minimising risk or obtaining expected returns above a specific threshold with minimal risk. An efficent frontier plot is a good visualisation of risk-return profiles for the different portfolio weightings: this is computed analytically (via optimisation), not via the Monte Carlo simulations.

<img width="845" height="547" alt="image" src="https://github.com/user-attachments/assets/f686ea9c-bcf8-4d08-88f1-3ddf79b00a21" />

Optimized Weights (Min Volatility):

CVS: 7.85%

ARES: 2.60%

LII: 10.89%

KIM: 61.90%

AXON: 4.76%

UAL: 2.74%

TTD: 9.25%

Weights that return at least 10% with the least risk:

CVS: 3.14%

ARES: 2.94%

LII: 5.51%

KIM: 52.92%

AXON: 23.21%

UAL: 12.00%

TTD: 0.27%

## Discussion
This project identifies the models which can be used to evaluate the performance of a stock portfolio whilst optimising investment strategy and mitigating risk, however these ideas can be extended to improve the reliability of our results by making extra considerations such as removing outliers in the clustering process alongside utilising correlation matrices or modifying the distribution of the simulated stock returns to be log-normal: we can check the fit of the historical returns to a normal distribution with QQ-plot to visualise where our assumptions break down. 

In relation to modern portfolio theory, we could also consider further diversification by including other asset types such as bonds and commodities. Implementing these would follow a similar process of determining a desired optimal weighting based on expected risk-return profiles. Since the optimisation process is primarily based off concepts relating to modern portfolio theory, there are limitations such as downside risk not being accounted for when selecting the weightings. Specifically for mean-variance optimisation, the volatility is symmetric and penalises upside as well as downside. To counter this, we could make use of the Sortino ratio (which uses downside deviation instead of total volatility) or CVaR optimisation. 

Another key consideration is that we have computed the Sharpe ratio on historical data to select the stocks, then also using historical data to optimise weights. This introduces selection bias, so to correct this we may opt to use out of sample testing. Essential we optimise weight on a set of "training" data from an earlier period and then evaluate the solution on a set of "test" data from a later period. Here we coudl make use of a fixed split in the data or rolling windows.
