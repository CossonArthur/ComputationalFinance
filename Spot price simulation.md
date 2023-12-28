# Merton 
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




# Kou

Params :
- $\mu$, $\sigma$, $\lambda_+$ , $\lambda_-$, $p$
$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + iu\lambda\left(\frac{p}{\lambda_+ - iu} - \frac{1-p}{\lambda_- + iu}\right)
$$



>[!notes]- Code
>```matlab
>function [S,SAV]=Asset_Kou_AV(Nsim,T,params,M,S0,r)
>% under Q for Exponential(X)
>
>% Nsim: number of simulations
>% T: maturity
>% params: parameters of the model
>% M: number of time steps
>% S0: initial value of the asset
>% r: risk-free rate
>
>
>% discretization
>dt=T/M;
>
>% parameters
>sigma=params(1);
>lambda=params(2);
>p=params(3);
>lambdap=params(4);
>lambdam=params(5);
>
>% characteristic function
>Psi=@(u) -sigma^2*u^2/2+1i*u*lambda*...
>    (p/(lambdap-1i*u)-(1-p)/(lambdam+1i*u));
>
>drift=r-Psi(-1i);
>
>X=zeros(Nsim,M+1); XAV=zeros(Nsim,M+1);
>% Sample Number of Jumps in each time-bucket
>Ndt=icdf('Poisson',rand(Nsim,M),lambda*dt);
>% Sample Continuous Component
>Z=randn(Nsim,M);
>for i=1:M % time loop
>    % continuous part
>    X(:,i+1)=X(:,i)+drift*dt+sigma*sqrt(dt)*Z(:,i);
>    XAV(:,i+1)=XAV(:,i)+drift*dt-sigma*sqrt(dt)*Z(:,i);
>    for j=1:Nsim % simulation loop
>        if Ndt(j,i)>0
>            % sample the jump positive/negative prob
>            Jpn=rand(Ndt(j,i),1);
>            % number of positive/negative jumps
>            Jp=sum( Jpn<p );
>            Jn=Ndt(j,i)-Jp;
>            % sample positive jump sizes
>            Y=0; YAV=0;
>            if Jp>0
>                u=rand(Jp,1);
>                Y=sum(icdf('Exponential',u,1/lambdap));
>                YAV=sum(icdf('Exponential',1-u,1/lambdap));
>            end
>            % sample Negative jump sizes
>            if Jn>0
>                u=rand(Jn,1);
>                Y=Y-sum(icdf('Exponential',u,1/lambdam));
>                YAV=YAV-sum(icdf('Exponential',1-u,1/lambdam));
>            end
>            % add jump
>            X(j,i+1)=X(j,i+1)+Y;
>            XAV(j,i+1)=XAV(j,i+1)+YAV;
>        end
>    end
>end
>S=S0*exp(X);  SAV=S0*exp(XAV);
>```

