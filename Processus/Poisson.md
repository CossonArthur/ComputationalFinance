# Introduction to Poisson Process

The Poisson Process is a mathematical model that describes the occurrence of rare and independent events over time. It is widely used in various fields, including queuing theory, telecommunications, and finance. The key characteristic of a Poisson Process is the constant rate of event occurrences within non-overlapping intervals.

## Poisson Process Definition

A stochastic process \(N(t)\) is a Poisson Process with rate parameter \(\lambda\) if, for any $(t \geq 0)$, the following conditions hold:

1. **Independence:** The number of events in non-overlapping intervals is independent.
   
2. **Stationarity:** The probability of a certain number of events occurring in a given interval depends only on the length of the interval, not on its starting point.

3. **Time Homogeneity:** The rate of events occurring is constant over time.

## Poisson Process Probability Distribution

The probability mass function (PMF) of a Poisson-distributed random variable \(X\) representing the number of events in a fixed interval is given by:

$$ P(X = k) = \frac{e^{-\lambda t} (\lambda t)^k}{k!} $$

Where:
- \( k \) is the number of events.
- \( \lambda \) is the average rate of events per unit time.
- \( t \) is the length of the interval.

## Poisson Process Intensity Function

The intensity function, denoted by \( \lambda(t) \), represents the rate of event occurrences at time \( t \). In a homogeneous Poisson Process, \( \lambda(t) \) is a constant:

\[ \lambda(t) = \lambda \]

In a non-homogeneous Poisson Process, \( \lambda(t) \) can vary over time, providing a more flexible model for real-world scenarios.

The Poisson Process serves as a fundamental tool for modeling rare and random events, offering a simple yet powerful framework for probability and statistics.
