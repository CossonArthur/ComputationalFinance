# Definition
Monte Carlo simulation is a computational technique that uses random sampling to estimate random variable.

**Basic Formula:**
The basic idea of Monte Carlo simulation is to use random sampling to approximate numerical results. The basic formula for a Monte Carlo estimate is often represented as follows:

$$
\tilde \theta = \frac{1}{N} \sum_{i=1}^{N} f(x_i)
$$

where:
- N is the number of random samples,
- $x_i$ represents a random sample from the input distribution,
- $f(x_i)$ is the function that evaluates the outcome for the given sample.

# Confidence Interval

A confidence interval provides a range of values that is likely to contain the true value of an unknown parameter. In the context of Monte Carlo simulations, confidence intervals can be calculated to quantify the uncertainty associated with the estimated results.

**Procedure for Confidence Interval Calculation:**
1. **Generate Samples:** Perform the Monte Carlo simulation to generate a large number of random samples.

2. **Calculate Estimates:** For each sample, calculate the desired outcome or parameter of interest using the simulation model.

3. **Compute Confidence Interval:** Use statistical methods to compute the confidence interval around the estimated result. One common approach is to use the sample mean and standard deviation to construct a confidence interval.

   The formula for a confidence interval for the mean (\(\mu\)) might be:

   $$
   CI = \tilde \theta \pm Z_{1-\alpha/2} \frac{\sigma}{\sqrt{N}}
   $$

   where Z is inverse normal distribution at $1-\alpha/2$

>[!Notes]- Code
> ```matlab
> [price, ~, CI] = normfit(disc_payoff) % CI at 95%
> or
> alpha=0.05;
> z = norminv(1-alpha/2);
>```


# Performance 
To improve the confidence of the estimation, there two levers :
- increase $N$
- decrease $\sigma$

## N increase
The first one consist in increasing the number of samples in the distribution to better approximate the real value.

## Variance reduction
The second one consist in decreasing the variance to better approximate the real value.
### Antithetic Variables
Let's consider a scenario where you are simulating a random variable X. The antithetic variable X′ is generated to be **negatively correlated** with X. The average of X and X′ should remain constant.

**Process:**
*Generate Pairs*
- For each random variable you're simulating, generate a pair of negatively correlated variables from the same distribution
- The correlation ensures that the average of the pairs remains constant.
*Calculate Average*
- For each pair, calculate the average of the two variables (X+X')/2

### Control variable
The idea is to use a function on which property can be computed through a known formula and which is close to the function that we want to approximate.

Suppose we know the expected value of another functional f of X, such that f and g are one "close" to the other.
$$
\begin{align}
\tilde \theta_{CV} &= \frac1n \sum_{i=1}^n g(Xi ) + \alpha (f(X_i) - E[f(X)])\\
\alpha &= \frac{\text{Cov}(g(X),f(X))}{\text{Var}(f(X))}
\end{align}
$$

