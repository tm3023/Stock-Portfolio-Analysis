# Stock Portfolio Analysis 
Mathematical methods are used to build a stock portfolio from S&amp;P 500 stocks. Monte Carlo simulations are then used to evaluate the performance of the portfolio before and after weighting optimisation. An efficient frontier is used to find weightings that meet certain perfomance criteria.

## Stock Selection for Portfolio

We may build a portoflio mannualy with stocks an investor is already interested in, however we have automated this process by exctracting a random sample tickers from a table of S&P 500 stocks on wikipedia and then using k-means clustering to sort the stocks by specific attributes. We must decide on the number of clusters to assign the stocks to, so we we may use inertia as a measure of how well-fitting the clusters are. Lower inertia indicates a better fit.
<Figure size 800x500 with 1 Axes><img width="686" height="470" alt="image" src="https://github.com/user-attachments/assets/7653bc79-bec4-4eea-bb17-45a5c783f906" />
