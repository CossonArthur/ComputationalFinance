# Probabilité

**Characteristic function :** Define a probability law, is equal to the Fourier transformation of the probability density function

# Stochastic process

## Brownian Motion

$B_t$ is a Brownian process  : $B_{t} \sim N(0, \sqrt{t})$

>[!notes]- Code
>```matlab
>  Bt =  sqrt(t)*randn
>```

## Jumps


>[!notes]- Code
>``` matlab
>lambda = 1; T = 1;
>
>Nt = icdf('Poisson',rand(1,1),lambda*t); % distribution, size, params
>jumps = sort(rand(1,Nt))*T
>
>```

>[!notes]- Code without using Poisson distribution function
>``` matlab
>lambda = 1; T = 2;
>tmp = icdf('Exp', rand, 1/lambda);
>while sum(tmp) <= T
>	tmp = [tmp icdf('Exp', rand, 1/lambda)];
>end
>
>Nt = length(tmp)-1;
>jumps = cumsum(tmp(1:end-1));
>
>```


# SDE
## Arithmetic Brownian Motion

$$ dX_t = μdt + σdW_t $$
## Geometric Brownian Motion

$$ dX_t = \mu X_tdt + \sigma X_tdW_t $$
## Ornstein-Uhlenbeck (OU)

$$ dX_t = \alpha (\mu-X_tdt) + \sigma X_tdW_t $$
- $X_t$ is the value of the process at time t,
- $\theta$ is the speed of mean reversion,
- $\mu$ is the long-term mean or target value,
- $\sigma$ is the volatility of the process,
- $W_t$ is a Wiener process or Brownian motion at time t,
- $dt$ is the differential of time.

## Heston
$$
\begin{aligned}
dS(t) &= \sqrt{v(t)} S(t) dW_1(t) \\
dv(t) &= \kappa (\theta - v(t)) dt + \sigma \sqrt{v(t)} dW_2(t)
\end{aligned}
$$
