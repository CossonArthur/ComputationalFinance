
# Poisson Process Basics

- **Poisson Process:** A stochastic process modeling the number of events occurring in fixed intervals of time or space.

- **Parameter ($\lambda$):** The average rate of events per interval.

# Countdown Process

- **Countdown Probability:** The probability of having exactly $k$ events in the remaining time until the countdown ends is given by the Poisson distribution: $P(X = k) = \frac{e^{-\lambda t} (\lambda t)^k}{k!}$, where $X$ is the number of events, $t$ is the remaining time, and $\lambda$ is the average rate.

- **Cumulative Countdown Probability:** The probability of having at most $k$ events in the remaining time is given by: $P(X \leq k) = \sum_{i=0}^{k} \frac{e^{-\lambda t} (\lambda t)^i}{i!}$.
