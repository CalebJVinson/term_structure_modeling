***Continuously updating model while reading associated texts.***

# Term-Structure Modeling

For this project, I wanted to work with term structures of interest rates and various other methods, tools, and processes that I had seen utilized in other research publications.

My primary reference for understanding and construction was Chapter 7 of Dynamic Asset Pricing Theory by Darrell Duffie titled Term-Structure Models. I found this chapter to be interesting due to its expansion on factors from the initially provided models. Additionally, I supplemented this with Chapter 10 of Stochastic Calculus for Finance II by Steven Shreve, which was titled the same as the Duffie text.

When considering the structure of our model we must understand that the market has a yield curve rather than operating from a single rate. Below is a summary from 10.1 of the SCFII text, which I found to be more informative on using the affine models before jumping into the Heath-Jarrow-Morton framework. Primarily, the two differ in focus as the affine models operate bottom-up starting with short rate changes that evolve toward the yield curve, while the HJM framework focuses operates top-down using forward rates.

## Bootstrapping and yield curves

When considering a single zero-coupon bond paying 1 at maturity, we let the price for each period (starting at 0) be described by $B(0,T_j)$ where *T* is a set of dates s.t. $0=T_0 < T_1 < T2 < ... <T_n$ where at each period *T_i* we receive a coupon payment *C*. These are fixed payments $C_1,C_2,...C_j$ which operate as interest payments, which also includes an interest payment plus the principal at $C_j$. We can then write the price at time zero as:

$$\sum_{i=1}^j C_i B(0,T_i)$$

We can then use the time passing and payment information to recursively discover the appropriate price of the zero-coupon bonds from 0 to *n* by understanding the price of the bond maturing at the next period $T_1$ is representative of the price of the $T_1$ maturity bond over the payment at time $T_1$. This process can be applied iteratively into the next period up to *n*. By using this method we are *bootstrapping* to find the zero-coupon bond prices from coupon-paying bond prices.

Briefly, we can find the zero-coupon bond price, where the yield can be considered a continuous compounding of the interest rate over the lifetime of the bond.

$$\text{price of zero-coupon bond} = \text{face value} \times e^{-yield \times t_{maturity}} $$

From this, there is now a consideration that there is a developing yield curve rather than a singular interest rate. We can think of this as an interpolation of finite maturity-yield pairs observed from the market. The interest rate is sometimes called the short rate and is idealized as corresponding to the shortest maturity yield or the overnight rate offered by the government.

While I had already understood the discussion above, I developed a further understanding of prices from the treatment of zero-coupon bonds under the risk-neutral pricing formula for the affine yield models.

# Term Structure

If we set up a probability space $(\Omega,\mathscr{F},P)$ with a filtration $$\mathbb{F} = \{ \mathscr{F}_t : 0 \leq t \leq T \} $$ of B, where B is a standard Brownian motion of a dimension $d \geq 1 \in \mathscr{R}^d$, we can work with given information for the structure.

From the above description of the short rate, r, we have $\int{0}^{T}|r_t|dt < \inf$. At any time t, we could invest a single unit and achieve a market value at the future value based on $e^{\int{t}^{s}r_u du}$. Which is compounding as the unit reinvests continually at the rate *s*.

Assuming absence of arbitrage, we can use a probability measure Q with the property that any security with a dividend is a lump-sum payment of Z at s has a price of

$$E_t^{Q} \left[ e^{\int{t}^{s} -r_u du} \right] \times Z$$

where $E^Q$ denotes the $\mathscr{F_t}$-conditional expectation under Q. Z would also be $\mathscr{F_t}$-measurable so the above is well defined. Letting $Z=1$, the price at *t* of the zero-coupon bond that matures at *s* is

$$ \Lambda_{t,s} \equiv E_t^{Q} \left[ e^{\int{t}^{s} -r_u du} \right]$$

This process is known as the discount function or *the term structure of interest rates*. The term structure is usually in terms of the yield curve where the continuously compounding yield, $y_{t,\tau}$, is defined by 

$$ y_{t, \tau} =  - \frac{log(\Lambda_{t, t + \tau})}{\tau}$$

which can also be represented in terms of forward interest rates. In the models, the short rate is modeled in terms of the standard Brownian motion under Q that comes from Girsanov's Theorem.

## One-Factor Term-Structure Models

$$dr_t = \mu(r_t, t) dt + \sigma(r_t, t) dB_t^{Q}$$

The short rate in the equation is the only factor on which the current yield curve of the depdends. So, we can write a price based on t and s, $\Lambda_{t,s} = F(t,s,r_t)$ for a fixed *F*: $[0,T] \times [0,T] \times \mathscr{R} \rightarrow \mathscr{R}$.


Each model is the sprecial case of an SDE:

$$ dr_t = \left[ K_0(t) + K_1(t)r_t + K_2(t) r_t log(r_t)\right] dt + \left[H_0(t) + H_1(t)r_t\right]^v dB_t^{Q}$$

where $K_0, K_1, K_2, H_0,\text{and } H_1$ are continous functions on 0 to T and $v$ is the exponent from 0.5 to 1.5. Each model we discuss has different forms with inclusions of some of the coefficients and differences in the exponent, *v*. The Cox-Ingersoll-Ross(CIR) model has non-zero values for $K_0, K_1, \text{and } H_1$ and has a $v = 0.5$. The Pearson-Sun model is the same as CIR but with the inclusion of $H_0$ with the same 0.5 exponent. Considering models where $v = 1$, we have the Dothan, Merton(Ho-Lee), Vasicek, and Black-Karasinski. Additionally at $v = 1.5$ we have the Constantides-Ingersoll model. We will discuss each of these, and we model some of them in the R-Markdown code.

#### $-K_1$ : Mean Reversion
With this coefficient, a negative value can be viewed as a mean-reversion parameter, so that high or low short rates generates low or high drift.

### Time varying coefficients

Some of the model differences are based purely on whether coefficients are allowed to vary or remain constant. For example, the *Merton model* of the term structure is called *Ho-Lee model* if coefficients are allowed to vary with time. 

# Describing Models

We can first describe affine models (linear plus constant models), starting with single-factor models. The affine class of models have $K_2 = 0$ and $v = 0.5$, which includes the Vasicek model where $H_1 = 0$, the CIR model where $H_0 = 0$, the Merton(Ho-Lee) model where $K_1 = H_1 = 0$, and the Pearson-Sun model.

## Affine class

### Vasicek interest rate model

$$dR(t) = (\alpha - \beta R(t))dt + \sigma dW(t)$$

where $R(t)$ is the interest rate process and $\alpha, \beta, \text{and }, \sigma$ are positive constants. 


## Time Varying Coefficients
### Hull - White Model

$$ dR(t) = (a(t) - b(t) R(t)) dt + \sigma(t) d \widetilde{W}(t)$$

### Cox-Ingersoll-Ross (CIR) interest rate model

$$dR(t) = (a-b R(t)) dt + \sigma \sqrt{R(t)} d \widetilde{W}(t)$$ 

## Two Factor Models
## Heath-Jarrow-Morton Framework
