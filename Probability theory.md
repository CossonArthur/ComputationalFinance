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
Let $X_t$ be a Lévy process ($\gamma$, A, $\nu$) on $R^d$,
$$
\Phi_{X_t}(z) = E[e^{iz\cdot X_t}] = e^{t \cdot \Psi(z)}, \quad \forall z \in R^d
$$
$\Psi$ is called the characteristic exponent 
$$
\Psi(z) = i\gamma\cdot z - \frac{1}{2}z^TAz+ \int_{R^d} (e^{iz\cdot x} -1-iz \cdot x 1_{|x|\leq 1})\nu(dx)
$$
To create new Levy process there is three methods 
## Linear transformation
Let $X_t \in R^d$​ be a Lévy process ($\gamma$, A,$\nu$), $M\in R^{n\cdot d}$ then $Y_t \in R^n$ is a Lévy process with  ($\tilde{\gamma}$, $\tilde{A}$,$\tilde{\nu}$) defined as:
$$ 
\begin{align*}
Y_t &=  M * X_t \\
\text{with,}\\
\tilde{A} &= MAM^T\\
\tilde{\nu}(B) &= \nu(\{x| Mx\in B \}) \\
\tilde{\gamma} &= Mγ + \int_{R^n} y(1_{|y|≤1}(y) − 1_{S_1} (y)) \cdot \tilde{\nu}(dy)\\
&\text{--> }S_1 = \{  Mx| |x|\leq 1\}
\end{align*}
$$



## Subordinator
 $S_t$ be a non-decreasing Lévy subordinator (0,$\rho$,$b$)
$$
E[e^{uS_t}] = e^{t l(u)} \text{ , }\forall u ≤ 0 \quad \text{, where } l(u) = bu + \int_0^{\infty}(e^{ux} − 1)\rho(dx)
$$
We call l(u) the Laplace exponent of S. Since process S is increasing it can
be interpreted as a “time deformation” and used to “time change” other Lévy processes as shown by the following theorem.

Let $X_t$​ be a Lévy process  ($\gamma$, A,$\nu$), and let $S_t$ be a non-decreasing Lévy subordinator ($\rho$,0,$b$). The subordinated Lévy process $Y_t$ is defined as:
$$ 
\begin{align*}
Y_t &= X_{S_t} \\
\text{where,}\\
&\tilde{A} = bA\\
&\tilde{\nu}(B) = b\nu(B) + \int_0^\infty p_s^X(B) \rho(ds)\\
&\tilde{\gamma} = b\gamma + \int_0^\infty \rho(ds) \int_{|x|\leq 1} x p_s^X(dx)\\
\end{align*}
$$

## Tilting & Tempering
By modifying the Lévy measure, we create a new Lévy process. A common way, is to multiply by an exponential constant. If there exists $\theta \in R^d$ such that $\int_{|x|≥1} e^{\theta x} \nu(dx) < \infty$ then the measure $\tilde{\nu}$ defined by

$$
\tilde{\nu}(dx) = e^{\theta dx} \nu(dx)
$$
This is called *exponential tilting*

In 1 dimension (d=1), 
$$
\tilde{\nu}(dx) = \nu(dx) (1_{x>0} e^{−\lambda_+x} + 1_{x<0}e^{−\lambda_−|x|})
$$

$\lambda_+$ and $\lambda_-$ are positive parameters, is also a Lévy measure and defines
a Lévy process whose large jumps are “tempered”

## Utils
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


## Heston
$$
\begin{aligned}
dS(t) &= \sqrt{v(t)} S(t) dW_1(t) \\
dv(t) &= \kappa (\theta - v(t)) dt + \sigma \sqrt{v(t)} dW_2(t)
\end{aligned}
$$



# Models 
$S_t = S_0 e^{X_t}$, with $X_t$ a Lévy process

## B&S
$W_t$ is a Weiner process
$$dX_t = \mu dt + \sigma dW_t$$

## Jump - Diffusion
$W_t$ is a Weiner process, $N(t) \sim P(\lambda)$, $Y_i$ i.i.d
$$dX_t = \mu dt + \sigma dW_t + \sum_{i=1}^{N(t)} Y_i$$

Two main modelisations are **Merton** and **Kou**

## Infinite Activity
Subordination t -> $\tilde S_t$ 
$$dX_t = \mu d\tilde S_t + \sigma dW_{\tilde S_t}$$

Two main modelisations are **VG** and **NIG**
