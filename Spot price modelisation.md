
# Q assumption
Q imply that the discounted asset price is a martingale.
--> Under $Q$ => $E_0^Q[e^{-rT}S_T] = S_0$ 

## General case 
the drift term $\mu dt$ in the SDE
$$
	\mu=r -\psi_X(-i)
$$

>[!notes]- Proof
>
>$E^Q[e^{-rT}S_T] = S_0$
>$E^Q[e^{-rT}S_0e^{\mu T+X_T}] = S_0$
>$e^{(\mu-r) T} \cdot e^{T\cdot \psi_X(-i)}] = 1$
>$\mu-r +\psi_X(-i) = 0$
>$\mu=r -\psi_X(-i)$
>
>because $E[e^{X_T}] = \phi_{X_T}(-i) = e^{T\cdot \psi_X(-i)}$


## GBM
$$
	\mu=r -\frac{\sigma^2}{2}
$$

>[!notes]- Proof
>
>$E^Q[e^{-rT}S_T] = S_0$
>$E^Q[e^{-rT}S_0e^{\mu T+\sigma W_T}] = S_0$
>$e^{(\mu-r) T} \cdot E^Q[e^{\sigma W_T}] = 1$
>$e^{(\mu-r) T} \cdot e^{\frac{\sigma^2}{2} T}] = 1$
>$(\mu-r +\frac{\sigma^2}{2})=0$
>$\mu=r -\frac{\sigma^2}{2}$



# Diffusion

## Black & Scholes

$$
dX_t = \mu X_tdt + \sigma X_tdW_t
$$
**Pros:**
- Easy 
**Cons:**
- no smile in implied volatility, const
- $S_t \sim N(r-\frac{\sigma^2}2), \frac{\sigma^2}2)$, skew and kurtosis problem 

# Jump 

## Finite activity
### Merton 
Merton model the spot price as following the SDE:
$$
\begin{align*}
dX_t &= \mu_1 X_tdt + \sigma X_tdW_t + \sum^{N(t)}_{i=1} Yi \\
\text{where,}\\
Y_i &\sim N(\mu_J, \sigma_J^2) \\
N(t) &\sim Poisson(\lambda*t)\\
\end{align*}
$$

Params :
- $\mu$ , $\sigma$, $\lambda$, $\mu_J$ $\sigma_J$
$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + \lambda \left(\exp\left(-\frac{\sigma_J^2 u^2}{2} + i\mu_J u\right) - 1\right)
$$

>[!notes]- Code
>```matlab
>function [S,SAV]=Asset_Merton_AV(Nsim,T,params,M,S0,r)
>% under Q for Exponential(X)
>dt=T/M;
>sigma=params(1);
>lambda=params(2);
>muJ=params(3);
>sigmaJ=params(4);
>Psi=@(u) -sigma^2*u^2/2+lambda*(exp(-sigmaJ^2*u^2/2+1i*muJ*u)-1);
>drift=r-Psi(-1i);
>% Sample Number of Jumps in each time-bucket
>Ndt=icdf('Poisson',rand(Nsim,M),lambda*dt);
>% Sample Continuous Component
>Z=randn(Nsim,M);
>X=zeros(Nsim,M+1); XAV=zeros(Nsim,M+1);
>for i=1:M % time loop
>    % continuous part
>    X(:,i+1)=X(:,i)+drift*dt+sigma*sqrt(dt)*Z(:,i);
>    XAV(:,i+1)=XAV(:,i)+drift*dt-sigma*sqrt(dt)*Z(:,i);
>    for j=1:Nsim % simulation loop
>        if Ndt(j,i)>0
>            % sample jumps
>            z=randn(Ndt(j,i),1);
>            Y=sum( muJ+sigmaJ*z );
>            YAV=sum( muJ-sigmaJ*z );
>            % add jumps
>            X(j,i+1)=X(j,i+1)+Y;
>            XAV(j,i+1)=XAV(j,i+1)+YAV;
>        end
>    end
>end
>
>S=S0*exp(X);
>SAV=S0*exp(XAV);
>end
>```

### Kou
Kou model the spot price as following the SDE:
$$
\begin{align*}
dX_t &= \mu_1 X_tdt + \sigma X_tdW_t + \sum^{N(t)}_{i=1} Yi \\
\text{where,}\\
N(t) &\sim Poisson(\lambda*t)\\
\end{align*}
$$

Params :
- $\mu$, $\sigma$, $\lambda_+$ , $\lambda_-$, $p$
$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + iu\lambda\left(\frac{p}{\lambda_+ - iu} - \frac{1-p}{\lambda_- + iu}\right)
$$

## Infinite Activity
### VG
$$
\begin{align}
dX_t &= \theta \kappa dS_t + \sigma \sqrt{S_t} dW_t\\
dS_t &\sim Gamma(\frac{dt}{\kappa})
\end{align}
$$
Then the path is $X_t = \sum_i^t dXi$
```matlab
icdf('Gamma', rand, params);
```

### NIG

$$
\begin{align}
dX_t &= \theta \kappa dS_t + \sigma \sqrt{S_t} dW_t\\
dS_t &\sim Inverse Gaussian(\frac{dt}{\kappa})
\end{align}
$$
Then the path is $X_t = \sum_i^t dXi$
```matlab
icdf('InverseGaussian', rand, params);
```

### Extended 
If we include a Brownian Motion, $\hat \sigma$. Then ($\gamma$, $\hat \sigma$, $\nu$) is the new Lévy process.
--> Actually useless as Infinite jump approximate a Brownian Motion.


# Stochastic volatility model
Those models don't assume $\sigma = \text{const}$. They assume $\sigma(t)$ is a positive stochastic process, often mean reverting. 

This model better fit the implied volatility in reality:
- Stochastic volatility is used for **long maturity** 
- Exponential Lévy ( jumps) is used for **short maturity** 

*Mean reverting*: the process doesn't diverge but oscillate around a mean value. Modeled with $d\sigma_t = \alpha(\beta - \sigma_t)dt + ...\text{(stochastic valatility model)}$
- $\beta$: reversing mean
- $\alpha$: speed of mean reversing

## Diffusion


$$
\begin{align}
dX_t &= \mu X_tdt + \sigma X_tdW_t&\\
\sigma_t &= f(y(t)) \quad & f \text{ positive function}\\
dy(t) &= \lambda(\eta-y(t))dt + \text{ ... } d\hat W(t)&\\
d\hat W_t\cdot dW_t &= \rho \quad & \text{correlation coef}
\end{align}
$$

### Hull-White 
$$
\begin{align}
\rho &= 0\\
f(t) &= \sqrt{y}\\
dy(t) &= C_1 y(t) dt + C_2 y(t) d\hat W(t)\quad GBM
\end{align}
$$

### Scott
$$
\begin{align}
\rho &= 0\\
f(t) &= \sqrt{y}\\
dy(t) &= \lambda( \eta - y(t)) dt +  \tilde \beta d\hat W(t)\quad \text{Gaussian OU}
\end{align}
$$
### Heston
$$
\begin{align}
\rho &\neq  0\\
f(t) &= \sqrt{y}\\
dy(t) &= \lambda( \eta - y(t)) dt +  \tilde \beta \sqrt{y(t)} d\hat W(t)\quad CIR
\end{align}
$$

**THR ( Fellman condition)**
$\theta < 2\lambda\eta$ => $p(y(t)<0) = 0$
$\longrightarrow$ can use Euler schema

$\theta > 2\lambda\eta$ => $p(y(t)=0) > 0$
$\longrightarrow$ can't use Euler schema because of $\sqrt{y(t)}$


## Jump - Diffusion

### Bates
$$
\begin{align}
dX_t &= (\mu-\frac12 X_t) dt + \sigma X_t dW_t + dZ_t&\\
\sigma_t &= \sqrt{y(t)} \quad & f \text{ positive function}\\
dV_t &= \lambda(\eta-V_t)dt + \theta \sqrt{V_t} d\hat W(t)&\\
d\hat W_t\cdot dW_t &= \rho dt \quad & \text{correlation coef}\\
dZ_t &\sim Compound Poisson(\lambda)
\end{align}
$$
$dZ_t$ : the lognormal  jump-price is $\sim N(log(1+K)- \delta^2/2,delta^2)$


### BNS (Barndorff-Nielsen-Shepard)
