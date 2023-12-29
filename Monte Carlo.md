**Presentation:**
Monte Carlo simulation is a computational technique that uses random sampling to estimate complex mathematical results. It is named after the Monte Carlo Casino in Monaco, known for its games of chance and randomness.

**Basic Formula:**
The basic idea of Monte Carlo simulation is to use random sampling to approximate numerical results. In the context of finance, physics, engineering, or other fields, it involves generating a large number of random samples to estimate the distribution of an outcome or to solve a problem that might be deterministic in principle but is too complex to solve analytically.

The basic formula for a Monte Carlo estimate is often represented as follows:

$\tilde \theta = \frac{1}{N} \sum_{i=1}^{N} f(x_i)$

where:
- N is the number of random samples,
- $x_i$ represents a random sample from the input distribution,
- $f(x_i)$ is the function that evaluates the outcome for the given sample.

### Confidence Interval

A confidence interval provides a range of values that is likely to contain the true value of an unknown parameter. In the context of Monte Carlo simulations, confidence intervals can be calculated to quantify the uncertainty associated with the estimated results.

**Procedure for Confidence Interval Calculation:**
1. **Generate Samples:** Perform the Monte Carlo simulation to generate a large number of random samples.

2. **Calculate Estimates:** For each sample, calculate the desired outcome or parameter of interest using the simulation model.

3. **Compute Confidence Interval:** Use statistical methods to compute the confidence interval around the estimated result. One common approach is to use the sample mean and standard deviation to construct a confidence interval.

   The formula for a confidence interval for the mean (\(\mu\)) might be:

   $IC = \tilde \theta \pm Z \frac{\sigma}{\sqrt{N}}$

   where Z is inverse normal distribution 

>[!Notes]- Code
> ```matlab
> [price]
> or
> alpha=0.05;
> z = norminv(1-alpha/2);
>```