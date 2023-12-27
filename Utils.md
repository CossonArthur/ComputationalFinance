# Brownian Motion

$B_t$ is a Brownian process  : $B_{t} \sim N(0, \sqrt(t))$

>[!notes]- Code
>```matlab
>  Bt =  sqrt(t)*randn
>```


# SDE
## Arithmetic Brownian Motion

$$ dX_t = μdt + σdW_t $$
## Geometric Brownian Motion

$$ dX_t = \mu X_tdt + \sigma X_tdW_t $$
## Ornstein-Uhlenbeck (OU)

$$ dX_t = \alpha (\mu-X_tdt) + \sigma X_tdW_t $$
- X(t) is the value of the process at time tt,
- θ is the speed of mean reversion,
- μ is the long-term mean or target value,
- σ is the volatility of the process,
- W(t) is a Wiener process or Brownian motion at time tt,
- dt is the differential of time.

## Heston
$$
\begin{aligned}
dS(t) &= \sqrt{v(t)} S(t) dW_1(t) \\
dv(t) &= \kappa (\theta - v(t)) dt + \sigma \sqrt{v(t)} dW_2(t)
\end{aligned}
$$
