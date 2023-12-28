# Probability

**Characteristic function :** Define a random variable, is equal to the Fourier transformation of the probability density function

## Poisson
$$ 
P(X = k) = e^{-\lambda} \frac{\lambda^k}{k!}
$$
Proprieties :
- $X, Y \sim P(\lambda), P(\mu) => X+Y \sim P(\lambda + \mu)$ 

## Ito's Lemma
Suppose $X_t$ is an [Itô drift-diffusion process](https://www.wikiwand.com/en/Itô_calculus#Itô_processes "Itô calculus") that satisfies the stochastic differential equation $dX_t = \mu dt + \sigma dB_t$ where $B_t$ is a [Wiener process](https://www.wikiwand.com/en/Wiener_process "Wiener process").

If $f(t,x)$ is a smooth function, then the process $Y_t=f(t,X_t)$ follows the stochastic differential equation:
$$
dY(t) = \left( \frac{\partial f}{\partial t} + \mu \frac{\partial f}{\partial x} + \frac{1}{2} \sigma^2 \frac{\partial^2 f}{\partial x^2} \right) dt + \sigma \frac{\partial f}{\partial x} dW(t)
$$
For the GBM: $f(S_t) = \log(S_t/S_0)$
$$
S_t = e^{\left(\mu - \frac{\sigma^2}{2}\right)t + \sigma B_t}
$$

# Stochastic process

## Subordinator
Allow to create complex stochastic process

Let $X_t$​ be a Lévy process, and let $L_t$ be a non-decreasing Lévy subordinator. The subordinated Lévy process $Y_t$ is defined as:

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

$$ dX_t = \mu dt + \sigma dW_t $$
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
