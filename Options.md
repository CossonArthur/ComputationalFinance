# Pricing
The price depends of the future value of the underlying asset, discounted by the return of the free rate asset:
$$
\begin{align*}
C &= E[e^{-rT}\Phi(S_T)]\\
\text{where,}\\
r &: \text{rate of risk-free asset}\\
T &: \text{time of execution}\\
\Phi &: \text{pay-off of the option}\\
\end{align*}
$$
*For simplicity reason, only call payoff is shown for the payoff*
$$
\begin{align*}
\text{Call} &: \Phi(S_T) = \max(S_t-K) \\
\text{Put} &: \Phi(S_T) = \max(K-S_t)
\end{align*}
$$

## Transformation

**Log price**: $x = \ln(\frac{S}{S_0})$

**Log moneyness**: $x = \ln(\frac{S}{K})$


## Monte Carlo (MC)
Monte Carlo simulation is a computational technique that uses random sampling to estimate random variable.

**Basic Formula:**
The basic idea of Monte Carlo simulation is to use random sampling to approximate numerical results. The basic formula for a Monte Carlo estimate is often represented as follows:

$$
\tilde \theta = \frac{1}{N} \sum_{i=1}^{N} f(x_i)
$$

where:
- N is the number of random samples,
- $x_i$ represents a random sample from the input distribution,
- $f(x_i)$ is the function that evaluates the outcome for the given sample.

In the case of spot price simulation Euler method is used : 
**Euler**
$$
X_{n+1} = X_n + dX_{n}
$$
**Milstein**
$$
X_{n+1} = X_n + dX_n + \frac{1}{2} b(X_n, t_n) \frac{\partial b}{\partial x}(X_n, t_n) \left(\Delta W_n^2 - \Delta t\right)
$$
### Confidence Interval

1. **Generate Samples:** Perform the Monte Carlo simulation to generate a large number of random samples.
2. **Calculate Estimates:** For each sample, calculate the desired outcome or parameter of interest using the simulation model.
3. **Compute Confidence Interval:** Use statistical methods to compute the confidence interval around the estimated result. One common approach is to use the sample mean and standard deviation to construct a confidence interval.

   The formula for a confidence interval for the mean $\tilde\theta$ might be:   $$
   CI = \tilde \theta \pm Z_{1-\alpha/2} \frac{\sigma}{\sqrt{N}}
   $$   where Z is inverse normal distribution at $1-\alpha/2$

>[!Notes]- Code
> ```matlab
> [price, ~, CI] = normfit(disc_payoff) % CI at 95%
> or
> alpha=0.05;
> z = norminv(1-alpha/2);
>```


### Performance 
To improve the confidence of the estimation, there two levers :
- **N increase**
	The first one consist in increasing the number of samples in the distribution to better approximate the real value.

- **Variance reduction**
	The second one consist in decreasing the variance to better approximate the real value.
	*Antithetic Variables*
	Let's consider a scenario where you are simulating a random variable X. The antithetic variable X′ is generated to be **negatively correlated** with X. The average of X and X′ should remain constant.

	*Generate Pairs*
	- For each random variable you're simulating, generate a pair of negatively correlated variables from the same distribution
	- The correlation ensures that the average of the pairs remains constant.
	*Calculate Average*
	- For each pair, calculate the average of the two variables (X+X')/2
	
	Possibilities to get negatively correlated : 
	- $X' = - X$
	- if $X = F^{-1}(p)$ then $X' = F^{-1}(1-p)$
	
	*Control variable*
	The idea is to use a function on which property can be computed through a known formula and which is close to the function that we want to approximate.
	
	Suppose we know the expected value of another functional f of X, such that f and g are one "close" to the other.	$$
	\begin{align}
	\tilde \theta_{CV} &= \frac1n \sum_{i=1}^n g(Xi ) + \alpha (f(X_i) - E[f(X)])\\
	\alpha &= -\frac{\text{Cov}(g(X),f(X))}{\text{Var}(f(X))}
	\end{align}
	$$
> [!code]- Code
> ```matlab
> %> f(x)=S(T)
>% 1. sample alpha
>S=Asset_Merton(Nsim/100,T,params,M,S0,r);
>f=S(:,end);
g=exp(-r*T)*max( f-K, 0);
VC=cov(f,g);
alpha=-VC(1,2)/VC(1,1);
% 2. compute the price
S=Asset_Merton(Nsim,T,params,M,S0,r);
f=S(:,end);
Ef=S0*exp(r*T);
g=exp(-r*T)*max( f-K, 0);
[Price,~,Price_CI]=normfit( g+alpha*(f-Ef) )
>```




## Carr-Madan
### Simple option
For stochastic process with jumps, there is no general formula to compute the option price, as the characteristic function of the underlying asset is not known. To compute the call option price  $C(k) = e^{−rT} E[(e^{rT+X_T} − e^k)^+]$, we use the Fourier Transform.
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
>% risk-neutral characteristic exponent>
>V=@(u) -sigma^2*u.^2/2+1i*u*lambda.*(p./(lambdap-1i*u)-(1-p)./(lambdam+1i*u));
>drift_rn=-V(-1i); % Drift Risk_neutral
>V=drift_rn*1i*v+V(v);
>```


### Convolution
Allow to compute the price of complex option like Barrier. As convolution in Fourier space is a multiplication. We calculate the Fourier transform, multiply, then take the inverse.
$$
C = E[e^{-rT}\Phi(S_T) \cdot 1_{\{x<D\}}]
$$
Transform this into a convolution -> Fourier -> mul -> inverse Fourier


## Partial Intregal Differential Equation (PIDE)
**M >> N**
With M the discretisation in time, and N discretisation in strike

### PIDE
#### Diffusion
$$
\begin{align}
&x = \ln(\frac{S}{S_0})\\
&\frac{∂V}{∂t}​+(r-\frac{σ^2}2)\frac{∂V}{∂x}​​+ \frac{σ^2}2 \frac{∂^2V}{∂x^2}​​−rV=0\\
&V = V(t,x)\\
&V(T,x) = \max(S_0e^x-K,0)
\end{align}
$$


### Bordery Conditions

We truncate the dimension for the price has the probability is almost always 0/
- $x_{\max} = (r-\frac{\sigma^2}2)T + 6 \sigma\sqrt{T}$
- $x_{\min} = (r-\frac{\sigma^2}2)T - 6 \sigma\sqrt{T}$

> [!proof]- Proof
> ![](Images/Pasted%20image%2020240101195659.png)
#### European

**Call**: 
$$
\begin{align}
V(t, x_{\min}) &= 0\\
V(t, x_{\max}) &= S_0 e^{x_{\max}} -Ke^{-r(T-t)}\\
\end{align}
$$

**Put**: 
$$
\begin{align}
V(t, x_{\min}) &= S_0 e^{x_{\min}} -Ke^{-r(T-t)}\\
V(t, x_{\max}) &= 0\\
\end{align}
$$
#### Barrier
| | |
| --- | --- |
|  |  |

### Difference method
For all the schema, $dW_t$ when discretise is $\sqrt{\Delta t} . N(0,1)$

**First order** : $f'(x)≈\frac{f(x+\Delta x)−f(x-\Delta x)​}{2\Delta x}$
**Second order** : $f''(x)≈\frac{f(x+\Delta x)+f(x-\Delta x)​-2f(x)​}{\Delta x^2}$
#### Euler
##### Explicit

$$
X_{n+1} = X_n + dX_{n}
$$

> [!code]- Code
> ```matlab
>%% Price an European Call Option
>% Explicit Euler - logprice pde
>% Black&Scholes Framework
>
> %% 1. Input
>S0=1; K=1; r=0.01; T=1; sigma=0.4;
>M=2000; % time
>N=400; % logprice
>%% 2. Grids
>xmin=(r-sigma^2/2)*T-sigma*6*sqrt(T);
>xmax=(r-sigma^2/2)*T+sigma*6*sqrt(T);
>x=linspace(xmin,xmax,N+1);
>dx=(xmax-xmin)/N;  dt=T/M;
>%% 3. Backward-In-Time Procedure
>% Maturity --> Payoff
>V=max( S0*exp(x)-K, 0);
>Vnew=zeros(size(V));
>for j=M-1:-1:0
>    Vnew(1)=0; % BC(xmin)
>    for i=2:N
>        Vnew(i)=V(i)+dt*(...
>            (r-sigma^2/2)*(V(i+1)-V(i-1))/(2*dx)...
>            +sigma^2/2*(V(i+1)-2*V(i)+V(i-1))/(dx^2)...
>            -r*V(i));
>    end
>    Vnew(N+1)=S0*exp(xmax)-K*exp(-r*(T-j*dt));% BC(xmax)
>    V=Vnew;
>end
>figure
>plot(S0*exp(x),V);
>xlabel('Spot price'); title('Call Price');
>Price=interp1(x,V,0,'spline')
>```
##### Implicit
Need to solve a linear system
$$
X_{n+1} = X_n + dX_{n+1}
$$

> [!code]- Code
> ```matlab
>%% Price an European Call Option
>% Implicit Euler - logprice pde
>% Black&Scholes Framework
>
>%% 1. Input
>S0=1; K=1; r=0.01; T=1; sigma=0.4;
>M=2000; % time
>N=1000; % logprice
>
>%% 2. Grids
>xmin=(r-sigma^2/2)*T-sigma*6*sqrt(T);
>xmax=(r-sigma^2/2)*T+sigma*6*sqrt(T);
>x=linspace(xmin,xmax,N+1)';
>dx=(xmax-xmin)/N;  dt=T/M;
>
> %.% 3. Construction of the matrix
>A=-(r-sigma^2/2)/(2*dx)+sigma^2/(2*dx^2);
>B=-1/dt-sigma^2/(dx^2)-r;
>C=(r-sigma^2/2)/(2*dx)+sigma^2/(2*dx^2);
>Matrix=spalloc(N+1,N+1,3*(N-1)+2);
>Matrix(1,1)=1;
>for i=2:N
>    Matrix(i,[i-1 i i+1])=[A B C];
>end
>Matrix(N+1,N+1)=1;
>
>%% 4. Backward-In-Time Procedure
>% Maturity --> Payoff
>V=max( S0*exp(x)-K, 0);
>BC=zeros(size(V));
>for j=M-1:-1:0
>    BC(end)=S0*exp(xmax)-K*exp(-r*(T-j*dt));
>    rhs=-[0;V(2:end-1);0]/dt+BC;
>    V=Matrix\rhs;
>end
>figure
>plot(S0*exp(x),V);
>xlabel('Spot price'); title('Call Price');
>Price=interp1(x,V,0,'spline')
> ```


##### Theta
Theta method is a generalisation of Euler, 
$\theta=0$ <=> Explicit and  $\theta=1$ <=> Implicit 
$$
X_{n+1} = X_n + (1-\theta) \cdot dX_{n} + \theta \cdot dX_{n+1}
$$
solve this system:  $\text{diag}(A,B,C) V_t = \text{diag}(A_h,B_h,C_h) V_{t+1}$ 

##### Crank-Nicholson
Theta schema with $\theta = 1/2$

# Types
## European

### Definition
A European option is a type of financial derivative that grants the holder the right, but not the obligation, to buy (call option) or sell (put option) an underlying asset at a predetermined price (strike price) on or before the expiration date.
- **Expiration Date:** European options can only be exercised at the expiration date, as opposed to American options, which can be exercised at any time before expiration.
- **Settlement:** Settlement of European options occurs at the expiration date, where the option holder either exercises the option or allows it to expire worthless.
- **Advantages:** European options simplify decision-making for investors, as they eliminate the need for continuous monitoring and decision-making throughout the option's life.

### Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$
### Pricing
> [!code]- MC code
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```


>[!notes]- Black & Scholes
>![Black & Scholes](Black%20&%20Scholes.md)


## American 
### Definition
An American option is a type of financial derivative that grants the holder the right, but not the obligation, to buy (call option) or sell (put option) an underlying asset at a predetermined price (strike price) at any time before or on the expiration date.
- **Exercise Flexibility:** Unlike European options, American options provide flexibility, allowing the holder to exercise the option at any point before or on the expiration date.
- **Expiration Date:** Similar to European options, American options have an expiration date, but the key distinction lies in the flexibility of exercising the option prior to this date.
- **Settlement:** Settlement for American options can occur at any time before or on the expiration date, giving the holder the advantage of choosing an optimal exercise time.
- **Advantages:** The flexibility in exercising American options provides enhanced opportunities for risk management and profit realization compared to European options.

### Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$
### Pricing
> [!code]- MC code
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
to be modified


## Asian 
### Definition
An Asian option is a type of financial derivative whose payoff is determined by the average price of the underlying asset over a specified period, rather than the spot price at a single point in time.
- **Averaging Period:** The key feature of Asian options is the averaging mechanism, where the payoff is influenced by the average price of the underlying asset over a predetermined period, such as the entire option's life or a specific interval.
- **Floating or Fixed Strike:** Asian options can have either a floating or fixed strike price. For a fixed strike Asian option, the average price is compared to the strike price. In a floating strike Asian option, the payoff is based on the difference between the average price and the spot price.
- **Advantages:** The use of average prices tends to reduce the impact of short-term price fluctuations, making Asian options attractive for investors seeking to hedge against volatility.
### Discounted pay-off
$$
\begin{align}
\text{Fixed Strike} &: \Phi(S_T) = e^{-rT}\cdot \max(\text{mean}(S)-K,0)\\
\text{Floating Strike} &: \Phi(S_T) = e^{-rT}\cdot \max(S_T-\text{mean}(S),0)\\
\end{align}
$$

### Pricing
> [!code]- MC code
> Fixed Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( mean(S,2)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
>Floating Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-mean(S,2), 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```


## Lookback Option

### Definition

A Lookback option is a type of financial derivative that gives the holder the right, but not the obligation, to buy (call option) or sell (put option) the underlying asset at its highest or lowest price over a specified period. The unique feature of a Lookback option is that the payoff depends on the extreme price (maximum or minimum) reached by the underlying asset during the option's life.

**Types:**
-  **Fixed Lookback Option:** Pays the maximum (for a call) or minimum (for a put) price of the underlying asset over the option period.
- **Floating Lookback Option:** Pays the maximum (for a call) or minimum (for a put) price of the underlying asset at the option's expiration.
**Advantages:**
Lookback options provide investors with the opportunity to benefit from favorable price movements without being locked into a single price point.

### Discounted Payoff

The discounted payoff for a Lookback option depends on whether it is a call or a put option. For a call option, the discounted payoff is given by:

$$
\begin{align}
\text{Floating strike} &: \Phi(S_T)=e^{−rT}\cdot(S_T​−\min_{0\leq t\leq T}​S_t​)\\
\text{Fixed strike} &: \Phi(S_T)=e^{−rT}\cdot \max(S_\max​−K)
\end{align}
$$
### Pricing
> [!code]- MC code
> Floating strike:
>```matlab
>DiscPayoff_Call = exp(-r*T) * (S(:,end) - min(S, [], 2));
>[Price_Call, ~, Price_CI_Call] = normfit(DiscPayoff_Call);
>```
>
>Fixed strike
>```matlab
>DiscPayoff_Call = exp(-r*T) * (max(S, [], 2) - K);
>[Price_Call, ~, Price_CI_Call] = normfit(DiscPayoff_Call);
>```

## Barrier
### Definition
A barrier option is a type of financial derivative with a feature known as a barrier, which, when crossed, can alter the option's characteristics. Barrier options can be either knock-in or knock-out, depending on whether the barrier activates or deactivates the option.
- **Barrier Types:**
    - **Knock-In:** In a knock-in barrier option, the option becomes active (starts to exist) only if the underlying asset's price reaches or crosses a specified barrier level during the option's life.
    - **Knock-Out:** In a knock-out barrier option, the option becomes null and void if the underlying asset's price reaches or crosses a predetermined barrier level during the option's life.
- **Down-and-In, Up-and-In, Down-and-Out, Up-and-Out:**
    - **Down-and-In (DI):** The option becomes active if the price falls below the barrier.
    - **Up-and-In (UI):** The option becomes active if the price rises above the barrier.
    - **Down-and-Out (DO):** The option becomes null and void if the price falls below the barrier.
    - **Up-and-Out (UO):** The option becomes null and void if the price rises above the barrier.
- Advantages:** Barrier options are often used for risk management, allowing investors to tailor their exposure to specific price movements and market conditions.

### Discounted pay-off
The pay-off is can be European or Asian ....

### Pricing
Here European Option is used
> [!code]- MC code
> Down-and-In (DI)
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0) .\*(min(S,[],2)<D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
> Up-and-In (UI)
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0) .\*(max(S,[],2)>D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
>Down-and-Out (DO)
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0) .\*(min(S,[],2)>D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
> Up-and-Out (UO)
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0) .\*(max(S,[],2)<D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```



## Double Barrier
### Definition
A double barrier option is a type of financial derivative with a feature known as two barriers, which, when crossed, can alter the option's characteristics. Barrier options can be either knock-in or knock-out, depending on whether the barrier activates or deactivates the option.
- **Barrier Types:**
    - **Knock-In:** In a knock-in barrier option, the option becomes active (starts to exist) only if the underlying asset's price reaches or crosses one of the barrier levels during the option's life.
    - **Knock-Out:** In a knock-out double barrier option, the option becomes null and void if the underlying asset's price reaches or crosses one of the barrier levels during the option's life.
- Advantages:** Double Barrier options are often used for risk management, allowing investors to tailor their exposure to specific price movements and market conditions.

### Discounted pay-off
The pay-off is can be European or Asian...
### Pricing
Here European option is used
> [!code]- MC code
> Knock-In 
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0) .*(min(S,[],2)<D).*(max(S,[],2)>D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
> Knock-Out
> ```matlab
> D = 1 % barrier
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0).*(min((S,[],2)>D) .*(max(S,[],2)<D)
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```



## Basket
### Definition
A basket option is a type of financial derivative that provides the holder with the right, but not the obligation, to buy or sell a portfolio of underlying assets at a predetermined price (strike price) on or before the expiration date.
- **Multiple Underlying Assets:** Unlike single-asset options, basket options are linked to a basket or portfolio of underlying assets, which can include stocks, commodities, or other financial instruments.
- **Advantages:** Investors use basket options for risk management and to hedge against movements in a diversified portfolio, providing a more comprehensive approach to managing market exposure.
### Discounted pay-off
The payoff of each asset depends of the chosen option type.
$$
\Phi(\{s_T^k| k\}) = e^{-rT}\cdot \sum_k w_k \Phi(S_T^k)
$$
### Pricing
> [!code]- MC code
> Fixed Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,2)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>to be mdified
