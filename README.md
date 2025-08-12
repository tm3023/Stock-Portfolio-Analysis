# Stock Portfolio Analysis 
Mathematical methods are used to build a stock portfolio from S&amp;P 500 stocks. Monte Carlo simulations are then used to evaluate the performance of the portfolio before and after weighting optimisation. An efficient frontier is used to find weightings that meet certain performance criteria. These methods are beneficial for determining an optimal invetsement strategy and mitigating risk.

## Stock Selection for Portfolio
In the following we will build a portfolio of S&P 500 stocks and use yfinance to obtain historical data of the selceted stocks. We may build a portoflio manually with stocks an investor is already interested in, however we have automated this process by exctracting a random sample of tickers from a table of S&P 500 stocks on wikipedia and then using k-means clustering to sort the stocks by specific attributes. We must decide on the number of clusters to assign the stocks to, so we we may use inertia as a measure of how well-fitting the clusters are. Lower inertia indicates a better fit.
<Figure size 800x500 with 1 Axes><img width="686" height="470" alt="image" src="https://github.com/user-attachments/assets/7653bc79-bec4-4eea-bb17-45a5c783f906" />

Optimal number of clusters: 4

The elbow method is then used to determine the optimal value for k, past which values give diminishing returns of how well the stocks fit into the clusters. 
The top perfoming stocks by Sharpe ratio are then picked out from each cluster. In this case we have the following:

Selected Securities from Clusters:

['BLDR', 'URI', 'DHI', 'TDY', 'CSX', 'MAA', 'DVA', 'FDX']

## Monte Carlo Simulatons
Now we have our portfolio of stocks, we carry out Monte Carlo simulations to analyse the performance of the porfolio based on specified weightings. First we will apply a uniform weighting of the stocks in our portfolio where price paths of our portfolio are modelled under a multivariate normal distribution. The following shows the price paths of 1000 simulated portfolios over a period of 100 days:

<Figure size 640x480 with 1 Axes><img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/ec9b6992-1f0d-4b5b-8a90-29e1ee998df0" />

Using the ending values of the simulated portfolios, a histogram plot can be used to compute useful perfomance metrics.

<Figure size 1000x600 with 1 Axes><img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/a26f4005-fe43-4201-81fd-aeb5c896d222" />

Monte Carlo Portfolio Performance

Mean Return: 8.85%

Std Dev of Return: 16.25%

Value at Risk (95%): 14.55%

Conditional VaR (95%): 20.70%

Mean Max Drawdown: -13.09%

Importantly, we would like to optimise the perfomance of the portfolio in a specified way. Here we will achieve this by optimising the weighting of the stocks in the portfolio to maximise the Sharpe ratio. By defining the negative Sharpe ratio of a portfolio along with relevant bounds and constraints (i.e. each weight is a value between 0 and 1 where the sum off all weights is 1), ```scipy.optimize.minimize()``` returns our desired weighting:

Optimized Weights (Max Sharpe):

BLDR: 37.42%

URI: 13.55%

DHI: 12.67%

TDY: 24.89%

CSX: 0.00%

MAA: 11.47%

DVA: 0.00%

FDX: 0.00%

Then we can run Monte Carlo simulations using this weighting, keeping the number of simulations and time period constant:

<Figure size 640x480 with 1 Axes><img width="547" height="413" alt="image" src="https://github.com/user-attachments/assets/c529d944-dfd1-4026-90c8-c44158913d47" />

<Figure size 1000x600 with 1 Axes><img width="841" height="547" alt="image" src="https://github.com/user-attachments/assets/66d70d60-886e-42eb-a748-f5f15b0926f9" />

Monte Carlo Portfolio Performance:

Mean Return: 11.94%

Std Dev of Return: 22.20%

Value at Risk (95%): 19.93%

Conditional VaR (95%): 25.48%

Mean Max Drawdown: -16.82% 

Here we see we have imporved the expected retun of the porfolio at the cost of increased risk. We could optimise our weighting under different criteria to potentially mitigate this.

## Efficient Frontier
We also may also utilise an effiicent frontier to find weightings that meet other perfomance criteria such as minmising risk or obtaining expected returns above a specific threshold with minimal risk. An efficent frontier plot is a good visualisation of risk-return profiles for the different portfolio weightings.

<Figure size 1000x600 with 2 Axes><img width="821" height="547" alt="image" src="https://github.com/user-attachments/assets/2d2f94cd-f794-46b1-9137-4749d839e1fd" />

Note: The plot is labelled to annualised expected returns, however in this case we have set the period to 100 days as opposed to a year (252 days).

Optimized Weights (Min Volatility):

BLDR: 1.32%

URI: 0.55%

DHI: 10.82%

TDY: 21.32%

CSX: 11.80%

MAA: 31.22%

DVA: 12.37%

FDX: 10.61%

Weights that return at least 10% with the least risk:

BLDR: 21.78%

URI: 5.44%

DHI: 13.16%

TDY: 28.27%

CSX: 8.96%

MAA: 17.61%

DVA: 3.87%

FDX: 0.92%

## Discussion
This project identifies the models which can be used to evaluate the performance of a stock portfolio whilst optimising investment strategy and mitigating risk, however these ideas can be extended to imporove the reliability of our results by making extra considerations such as removing outliers in the clustering process or modifiying the dsitribution of the simulated stock returns to be log-normal. In relation to modern portfolio theory we could also consider further diversifaction by including other asset types such as bonds and commodities. Implementing these would follow a similar process of determining a desired optimial weighting based on expected risk-return profiles. Since the weighing optimisiation process is primarily based off of concepts relating to modern portfolio theory, there are limitations such as downside risk not being accounted for when selcting a weighting. To conter this we could incorporate a method for optimising the weightings based on max-drawdown as opposed to just volatility.
