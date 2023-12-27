# Composantes 
## Trend 


$$ dX_t = \gamma dt $$
## Random movement

$$ dX_t = \sigma dB_t $$
$dB_t$ is a Brownian process  : $B_{t} \sim N(0, \sqrt(t))$
>[!notes]- Code
>```matlab
>  Xt = ... + sigma * sqrt(dt)*randn
>```



[Poisson](Poisson.md)


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

# Jump 
$$ dX_t = \sum^{N(t)}_{i=1}  Y_i $$
### Merton 
$Y_i \sim N(\mu, \delta)$ 
$N(t) =  \sup \{ k | \sum^k_{i=1} T_i < t\}$ where $T \sim exp(\lambda)$ 


>[!notes]- Code 1
>```matlab
>  Ndt 
>```


### Kou

