# Introduction to Poisson Process

The Poisson Process is a mathematical model that describes the occurrence of rare and independent events over time. It is widely used in various fields, including queuing theory, telecommunications, and finance. The key characteristic of a Poisson Process is the constant rate of event occurrences within non-overlapping intervals.

## Poisson Process Definition

A stochastic process \(N(t)\) is a Poisson Process with rate parameter $\lambda$ if, for any $(t \geq 0)$, the following conditions hold:

1. **Independence:** The number of events in non-overlapping intervals is independent.
   
2. **Stationarity:** The probability of a certain number of events occurring in a given interval depends only on the length of the interval, not on its starting point.

3. **Time Homogeneity:** The rate of events occurring is constant over time.

## Poisson Process Probability Distribution

The probability mass function (PMF) of a Poisson-distributed random variable \(X\) representing the number of events in a fixed interval is given by:

$$ P(X = k) = \frac{e^{-\lambda t} (\lambda t)^k}{k!} $$

Where:
- \k is the number of events.
- $\lambda$  is the average rate of events per unit time.
- t is the length of the interval.

## Simulation 

``` matlab
lambda = 1; T = 1;

Nt = icdf('Poisson',rand(1,1),lambda*t); % distribution, size, params
jumps = sort(rand(1,Nt))*T

```


Without using Poisson distribution function in Matlab
``` matlab
lambda = 1; T = 2;

tmp = icdf('Exp', rand, 1/lambda);
while sum(tmp) <= T
	tmp = [tmp icdf('Exp', rand, 1/lambda)];
end

Nt = length(tmp)-1;
jumps = cumsum(tmp(1:end-1));
```
