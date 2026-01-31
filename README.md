# Stock Portfolio Analysis 
Mathematical methods are used to build a stock portfolio from S&amp;P 500 stocks. Monte Carlo simulations are then used to evaluate the performance of the portfolio before and after weighting optimisation. An efficient frontier is used to find weightings that meet desired performance criteria. These methods are beneficial for determining an optimal invetsement strategy and mitigating risk.

## Stock Selection for Portfolio
In the following we will build a portfolio of S&P 500 stocks and use Python's ```yfinance``` module to obtain historical data of the selceted stocks. We may build a diverse portfolio manually with stocks an investor is already interested in, however we have automated this process by exctracting a random sample of tickers from a table of S&P 500 stocks exctracted from wikipedia and then using k-means clustering to sort the stocks by specific attributes. We must decide on the number of clusters to assign the stocks to, so we we may use inertia as a measure of how well-fitting the clusters are. Lower inertia indicates a better fit.
<Figure size 800x500 with 1 Axes><img width="686" height="470" alt="image" src="https://github.com/user-attachments/assets/2c34f969-5cf2-4f20-9cb4-a56cc10dd6c7" />

Optimal number of clusters: 4

The elbow method is then used to determine the optimal value for k, past which values give diminishing returns of how well the stocks fit into the clusters. 
The top perfoming stocks by Sharpe ratio are then picked out from each cluster. In this case we have the following:

Selected Securities from Clusters:

['CVS', 'ARES', 'LII', 'KIM', 'AXON', 'UAL', 'TTD']

## Monte Carlo Simulatons
Now we have our portfolio of stocks, we carry out Monte Carlo simulations to analyse the performance of the porfolio based on specified weightings. First we will apply a uniform weighting of the stocks in our portfolio where price paths of our portfolio are modelled using the cumulative product of returns under a multivariate normal distribution $\mathcal{MVN}(\mu, \Sigma)$, where $\mu$ is a column vector such that each entry contains the historical mean returns of a stock and $\Sigma$ is the correseponding covaraince matrix for the returns. The following shows the price paths of 1000 simulated portfolios over a period of 100 days:

<img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/fc71383f-1dab-4a79-bea6-493d036d4655" />

Using the ending values of the simulated portfolios, a histogram plot can be used to compute useful perfomance metrics.

<img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/331f9250-e345-4352-9f90-422491a4d077" />

Monte Carlo Portfolio Performance

Mean Return: 4.63%

Std Dev of Return: 16.88%

Value at Risk (5%): 19.73%

Conditional VaR (5%): 25.60%

Mean Max Drawdown: -14.87%

Importantly, we would like to optimise the perfomance of the portfolio in a specified way. Here we will achieve this by optimising the weighting of the stocks in the portfolio to maximise the Sharpe ratio. By defining the negative Sharpe ratio of a portfolio along with relevant bounds and constraints (i.e. each weight is a value between 0 and 1 where the sum off all weights is 1), ```scipy.optimize.minimize()``` returns our desired weighting:

Optimized Weights (Max Sharpe):

CVS: 0.00%

ARES: 0.00%

LII: 0.00%

KIM: 2.15%

AXON: 61.75%

UAL: 36.10%

TTD: 0.00%

Then we can run Monte Carlo simulations using this weighting, keeping the number of simulations and time period constant:

<img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/ab24559b-aea9-43c8-b29b-ad86d04875e1" />

<img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/f63704a0-4605-4fd3-8dfb-6c2b694ace8c" />

Monte Carlo Portfolio Performance:

Mean Return: 22.48%


Std Dev of Return: 33.30%

Value at Risk (5%): 23.26%

Conditional VaR (5%): 30.26%

Mean Max Drawdown: -21.01%

Here we see we have imporved the expected retun of the porfolio at the cost of increased risk. We could optimise our weighting under different criteria to potentially mitigate this.

## Efficient Frontier
We also may also utilise an effiicent frontier to find weightings that meet other perfomance criteria such as minmising risk or obtaining expected returns above a specific threshold with minimal risk. An efficent frontier plot is a good visualisation of risk-return profiles for the different portfolio weightings.

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
This project identifies the models which can be used to evaluate the performance of a stock portfolio whilst optimising investment strategy and mitigating risk, however these ideas can be extended to imporove the reliability of our results by making extra considerations such as removing outliers in the clustering process alongside utilising correlation matrices or modifiying the distribution of the simulated stock returns to be log-normal: we can check check the fit of the historical returns to a normal distribution with QQ-plot to visulaize where our assumptions break down. In relation to modern portfolio theory we could also consider further diversifaction by including other asset types such as bonds and commodities. Implementing these would follow a similar process of determining a desired optimial weighting based on expected risk-return profiles. Since the optimisiation process is primarily based off of concepts relating to modern portfolio theory, there are limitations such as downside risk not being accounted for when selcting the weightings. To counter this we could incorporate a method for optimising the weightings based on max-drawdown as opposed to volatility.
