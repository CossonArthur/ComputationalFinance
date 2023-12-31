For stochastic process with jumps, there is not general formula to compute the option price, as the characteristic function of the underlying asset is not known. To compute the call option price  $C(k) = e^{−rT} E[(e^{rT+X_T} − e^k)^+]$, we use the Fourier Transform.

$$
\begin{align*}
C(K) &= \max(1 - e^{k-rT},0) + \frac{1}{\pi} \int_{0}^{\infty} \mathrm{Re}\left( e^{-ik\cdot u} \frac{e^{-irT\cdot u} (\phi_T(u-i) - 1)}{iu(1+iu)} \right)du \\
\text{where:} \\
& C(k) \text{ is the call option price}, \\
& k \text{ is the option strike price}, \\
& \phi(u) \text{ is the characteristic function}.
\end{align*}
$$


> [!code]- Code
>```matlab
```
>function [Price]=FFT_CM_Call_Kou(Strike,params,T,r,S0)
>% Price of a Plain Vanilla Call exploiting the Carr-Madan algorithm
>
>% discretization parameter
>Npow=14; N=2^Npow; A=800;
>
>% v-> compute integral as a summation
>eta=A/N; v=0:eta:A*(N-1)/N; v(1)=1e-22;
>% lambda-> compute summation via FFT
>lambda=2*pi/(N*eta);
>k=-lambda*N/2+lambda*(0:N-1);
>
>% Fourier transform of z_k
>CharFunc=@(v) exp(T*CharExp(v,params));
>
>Z_k=exp(1i*r*v*T).*(CharFunc(v-1i)-1)./(1i*v.*(1i*v+1));
>
>% Option Price
>w=ones(1,N); w(1)=0.5; w(end)=0.5;
>x=w.*eta.*Z_k.*exp(1i*pi*(0:N-1));
>z_k=real(fft(x)/pi);
>C=S0*(z_k+max(1-exp(k-r*T),0));
>K=S0*exp(k);
>
>
>% Remove outliers
>index=find( K>0.1*S0 & K<3*S0 );
>C=C(index); K=K(index);
>
>% Have a curve of C=f(K), so to get the price need interpolation
>Price=interp1(K,C,Strike,'spline');
>
>%>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
>% Characteristic function of the log-price for Kou
>function V=CharExp(v,params)
>% risk-neutral characteristic exponent
>sigma=params(1);
>lambda=params(2);
>p=params(3);
>lambdap=params(4);
>lambdam=params(5);
>
>V=@(u) -sigma^2*u.^2/2+1i*u*lambda.*(p./(lambdap-1i*u)-(1-p)./(lambdam+1i*u));
>drift_rn=-V(-1i); % Drift Risk_neutral
>V=drift_rn*1i*v+V(v);