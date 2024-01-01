# Diffusion

## Black & Scholes

$$
dX_t = \mu X_tdt + \sigma X_tdW_t
$$

# Jump


# Jump - Diffusion
Under $Q$, $E_0^Q[e^{-rT}S_T] = S_0$ which imply:
$$
	\mu=r -\frac{\sigma^2}{2}
$$

>[!notes]- Proof
>
>$E_0^Q[e^{-rT}S_T] = S_0$
>$E_0^Q[e^{-rT}S_0e^{\mu T+\sigma W_T}] = S_0$
>$e^{(\mu-r) T} \cdot E_0^Q[e^{\sigma W_T}] = 1$
>$e^{(\mu-r) T} \cdot e^{\frac{\sigma^2}{2} T}] = 1$
>$(\mu-r +\frac{\sigma^2}{2})=0$
>$\mu=r -\frac{\sigma^2}{2}$

## Merton 
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

## Kou
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



# Stochastic volatility model
Those models don't assume $\sigma = \text{const}$. $\sigma(t)$ is a positive stochastic process, often mean reverting.

*Mean reverting*: the process doesn't diverge but oscillate around a mean value. Modeled with $d\sigma_t = \alpha(\beta - \sigma_t)dt + ...\text{(stochastic valatility model)}$