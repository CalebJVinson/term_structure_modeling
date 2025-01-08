# Term-Structure Modeling

For this project, I wanted to work with term structures of interest rates and various other methods, tools, and processes that I had seen utilized in other research publications.

My primary reference for understanding and construction was Chapter 7 of Dynamic Asset Pricing Theory by Darrell Duffie titled Term-Structure Models. I found this chapter to be interesting due to its expansion on factors from the initially provided models. Additionally, I supplemented this with Chapter 10 of Stochastic Calculus for Finance II by Steven Shreve, which was titled the same as the Duffie text.

When considering the structure of our model we must understand that the market has a yield curve rather than operating from a single rate. Below is a summary from 10.1 of the SCFII text, which I found to be more informative on using the affine models before jumping into the Heath-Jarrow-Morton framework. Primarily, the two differ in focus as the affine models operate bottom-up starting with short rate changes that evolve toward the yield curve, while the HJM framework focuses operates top-down using forward rates.

## Bootstrapping and yield curves

When considering a single zero-coupon bond paying 1 at maturity, we let the price for each period (starting at 0) be described by $B(0,T_j)$ where *T* is a set of dates s.t. $0=T_0 < T_1 < T2 < ... <T_n$ where at each period *T_i* we receive a coupon payment *C*. These are fixed payments $C_1,C_2,...C_j$ which operate as interest payments, which also includes an interest payment plus the principal at $C_j$. We can then write the price at time zero as:

$$\sum_{i=1}^j C_i B(0,T_i)$$

We can then use the time passing and payment information to recursively discover the appropriate price of the zero-coupon bonds from 0 to *n* by understanding the price of the bond maturing at the next period $T_1$ is representative of the price of the $T_1$ maturity bond over the payment at time $T_1$. This process can be applied iteratively into the next period up to *n*. By using this method we are *bootstrapping* to find the zero-coupon bond prices from coupon-paying bond prices.

Briefly, we can find the zero-coupon bond price, where the yield can be considered a continuous compounding of the interest rate over the lifetime of the bond.

$$\text{price of zero-coupon bond} = \text{face value} \times e^{yield \times t_{maturity}} $$
