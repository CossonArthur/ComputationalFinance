
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


>[!notes]- Code
>```matlab
function Path=AssetB_S(S0,r,sigma,T,Ndates,Nsim)
>
dt=T/Ndates;
drift = r - sigma^2/2;
>  
X=zeros(Nsim,Ndates+1);
temp=randn(Nsim,Ndates);
 > 
for i=1:Ndates
    X(:,i+1) = X(:,i) + drift*dt + simga * sqrt(dt) * temp(:,i);
end
Path = S0*exp(X);
end
>```
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
>function S=Asset_Merton(Nsim,T,params,M,S0,r)
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
>X=zeros(Nsim,M+1);
>for i=1:M % time loop
>    % continuous part
>    X(:,i+1)=X(:,i)+drift*dt+sigma*sqrt(dt)*Z(:,i);
>    for j=1:Nsim % simulation loop
>        if Ndt(j,i)>0
>            % sample jumps
>            z=randn(Ndt(j,i),1);
>            Y=sum( muJ+sigmaJ*z );
>            % add jumps
>            X(j,i+1)=X(j,i+1)+Y;
>        end
>    end
>end
>
>S=S0*exp(X);
>end
>```

### Kou
Kou model the spot price as following the SDE:
$$
\begin{align*}
dX_t &= \mu_1 X_tdt + \sigma X_tdW_t + \sum^{N(t)}_{i=1} Yi \\
\text{where,}\\
N(t) &\sim \text{double exp}(\lambda*t)\\
\end{align*}
$$

Params :
- $\mu$, $\sigma$, $\lambda_+$ , $\lambda_-$, $p$
$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + iu\lambda\left(\frac{p}{\lambda_+ - iu} - \frac{1-p}{\lambda_- + iu}\right)
$$

>[!notes]- Code
>```matlab
function Path=AssetKou(params,S0,r,T,Ndates,Nsim)
>
sigma=params(1);
lambda=params(2);
p=params(3);
lambdap=params(4);
lambdam=params(5);
psi=@(u) -sigma^2*u^2/2+1i*u*lambda*(p/(lambdap-1i*u)-(1-p)/(lambdam+1i*u));
drift = r - psi(-1i);
dt=T/Ndates;
>
X=zeros(Nsim,Ndates+1);
temp=randn(Nsim,Ndates);
Nt=icdf('Poisson', rand(Nsim,Ndates), lambda*dt);
>
for i=1:Ndates
    X(:,i+1) = X(:,i) + drift*dt + simga * sqrt(dt) * temp(:,i);
    for j =1:Nsim
        if(Nt(j,i) > 0)
            Jpn = rand(N(j,i),1);
            Jp=sum( Jpn<p );
            Jn=Ndt(j,i)-Jp;
            % sample positive jump sizes
            Y=0;
            if(Jp >0)
                Y = Y + sum(icdf('Exp',rand(Jp,1), 1/lambdap));
            end
            if(Jn>0)
                Y = Y - sum(icdf('Exp',rand(Jn,1), 1/lambdam));
            end
            X(:,i+1) = X(:,i+1) + Y;
        end
    end
    Path = S0*exp(X);
end
end
>```


## Infinite Activity
### VG
$$
\begin{align}
dX_t &= \theta \kappa dS_t + \sigma \sqrt{S_t} dW_t\\
dS_t &\sim Gamma(\frac{dt}{\kappa})
\end{align}
$$

>[!notes]- Code
>```matlab
>function Path=AssetVG(params, S0,T,Ndates,Nsim)
>  
k = params(1);
theta = params(2);
r = params(3);
sigmaVG = params(4);
sigma = params(5);   % sigma != 0 (Extended)
>
psi = @(u) -log(1 + u^2*sigmaVG^2*k/2 -1i*theta*u*k)/k;
drift = r - psi(-1i);
dt=T/Ndates;
> 
X=zeros(Nsim,Ndates+1);
temp=randn(Nsim,Ndates);
tempNG=randn(Nsim,Ndates);
dS = k * icdf('Gamma', rand(Nsim, Ndates), dt/k,1);
for i=1:Ndates
    X(:,i+1) = X(:,i) + drift*dt + sigma*sqrt(dt).*temp(:,i) + theta* dS(:,i) + sigmaVG.*sqrt(dS(:,i)).*tempNG(:,i);
end
Path = S0*exp(X);
end
>```
### NIG

$$
\begin{align}
dX_t &= \theta \kappa dS_t + \sigma \sqrt{S_t} dW_t\\
dS_t &\sim Inverse Gaussian(\frac{dt}{\kappa})
\end{align}
$$
>[!notes]- Code
>```matlab
function Path=AssetNIG(params, S0,T,Ndates,Nsim)
>  
k = params(1);
theta = params(2);
r = params(3);
sigmaNIG = params(4);
sigma = params(5);   % sigma != 0 (Extended)
>  
psi = @(u) 1/k -1/k * sqrt(1+ u^2*sigmaNIG^2*k-2i*theta*u*k);
drift = r - psi(-1i);
dt=T/Ndates;
>  
X=zeros(Nsim,Ndates+1);
temp=randn(Nsim,Ndates);
tempNG=randn(Nsim,Ndates);
dS = icdf('InverseGaussian', rand(Nsim,Ndates),dt, dt^2/k)
for i=1:Ndates
    X(:,i+1) = X(:,i) + drift*dt + sigma*sqrt(dt).*temp(:,i) + theta* dS(:,i) + sigmaNIG.*sqrt(dS(:,i)).*tempNG(:,i);
end
Path = S0*exp(X);
end
>```

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
