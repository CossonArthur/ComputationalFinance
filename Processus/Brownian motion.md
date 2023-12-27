# Brownian Motion

## Definition

- **Brownian Motion:** A stochastic process describing the random motion of particles in a fluid, named after Robert Brown who observed pollen particles in water.

## Characteristics

- **Random Walk:** Brownian motion is a continuous-time random walk, where the particle's position changes randomly over time.

- **Markov Property:** The future movement of the particle is independent of its past movement, given its current position.

- **Gaussian Increments:** The increments of Brownian motion within a given time interval follow a normal (Gaussian) distribution.

## Mathematical Representation

- **Continuous-Time Model:** In one dimension, the position of a particle undergoing Brownian motion, denoted as $B(t)$, can be described by the stochastic differential equation: $\frac{dB(t)}{dt} = \sigma \frac{dW(t)}{dt}$, where $\sigma$ is the volatility and $W(t)$ is a Wiener process.

- **Discrete-Time Model:** In discrete time, the position at each step is determined by adding a random increment: $B_{n+1} = B_n + \sigma \cdot \sqrt{\Delta t} \cdot Z_{n+1}$, where $Z_{n+1}$ is a standard normal random variable and $\Delta t$ is the time step.

## Applications

- **Finance:** Brownian motion is widely used in finance to model the stochastic movements of asset prices in the Black-Scholes model.

- **Physics:** It serves as a fundamental model for the diffusion of particles in gases and liquids.

- **Economics:** Used to model fluctuations in economic variables over time.

## Example

- **Stock Price Movement:** If a stock price follows geometric Brownian motion, its price at time $t$ can be modeled as $S(t) = S_0 e^{(\mu - \frac{\sigma^2}{2})t + \sigma W(t)}$, where $S_0$ is the initial stock price, $\mu$ is the drift, $\sigma$ is the volatility, and $W(t)$ is a Wiener process.