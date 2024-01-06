# Monte Carlo
Predicting the path of N simulations -> taking the $\max({S_t-K| t})$. WRONG!
The previous method cannot be used without giving future information to determine current price.  It only give an upper bound. 

To do that a way is to go step by step backward in time:
$$
\begin{align}
IV_t &= \max(S_t-K,0)\\
IC_t &= E[e^{-r\Delta t}V_t|S_t] \approx \sum_k \alpha_{k,t} \cdot \psi_k(S_t)\\ 
V_t &= \max(IV_t, IC_t)
\end{align}
$$
$\alpha_{k,t}$ are obtain by doing a linear regression on all the simulations on their value at $t$

> [!NOTE] In the money
> Better to consider only the simulation $i$ such that $IV_{i,t}$ > 0


> [!code]- Code
> ```matlab
> r=0.04; T=1; M=40; N=1e6; sigma=0.4; S0=1; K=1;
S=AssetBS(r,sigma,S0,T,M,N);
S=S(:,2:end); % we do not consider t=0 for early exercise
dt=T/M;
%==== INITIALIZE =========================
Exercise_Time=M*ones(N,1);
Call=max(0,S(:,end)-K); %payoff
>
%==== BACKWARD-IN-TIME ===================
for t=M-1:-1:1
    Inmoney=find(S(:,t)>K);
    S_t=S(Inmoney,t);
    %-- Intrinsic Value
    IV=S_t-K;
    %-- Continuation Value
    %- Regression
    A=[ones(length(S_t),1), S_t, S_t.^2];
    b=Call(Inmoney).*exp(-r*dt*(Exercise_Time(Inmoney)-t));
    alpha=A\b;
    %- Continuation Value
    CV=A*alpha;
    %----------
    %== t is an exercise instant?
    Index=find(IV>CV);
    Early_Exercise=Inmoney(Index);
    % Update
    Call(Early_Exercise)=IV(Index);
    Exercise_Time(Early_Exercise)=t;
end
[price,~,CI]=normfit(Call.*exp(-r*dt*Exercise_Time))
> ```


# PDE - B&S

## SOR algorithm
SOR algorithm allow to solve a linear system : $Ax=b$; $\omega \in [0,2]$
$$
x_i^{(k+1)} = (1 - \omega) x_i^{(k)} + \omega \left( \frac{b_i - \sum_{j=1}^{i-1} a_{ij} x_j^{(k+1)} - \sum_{j=i+1}^{n} a_{ij} x_j^{(k)}}{a_{ii}} \right)
$$

> [!code]-  SOR algorithm code
> ```matlab
function ynew = PSOR(yold,A,b)
tol = 1e-4; Nmax = 50; omega = 1.5;
ynew = zeros(size(yold));
for k = 1:Nmax
for i = 1:length(yold)
if(i==1)
z = (b(i) - A(i,i+1)*yold(i+1))/A(i,i);
elseif (i==length(yold))
z = (b(i) - A(i,i-1)*ynew(i-1))/A(i,i);
else
z = (b(i) - A(i,i-1)*ynew(i-1) - A(i,i+1)*yold(i+1))/A(i,i);
end
ynew(i) =yold(i) + omega*(z-yold(i));
end
if(norm(ynew-yold, 'inf') < tol)
break
else
yold=ynew;
end
end
>```

## Price
To price the American option, the algorithm is the same as in the Euler Implicit, with only difference the solving of the linear system. We need to use the Modified SOR algorithm. 
$$
y_i^{(k+1)} = \max(\text{payoff}, x_i^{(k+1)}), \quad \forall i
$$

> [!code]-  modified SOR algorithm code
> ```matlab
function ynew = PSOR(yold,A,b,payoffatT)
tol = 1e-4; Nmax = 50; omega = 1.5;
ynew = zeros(size(yold));
for k = 1:Nmax
for i = 1:length(yold)
if(i==1)
z = (b(i) - A(i,i+1)*yold(i+1))/A(i,i);
elseif (i==length(yold))
z = (b(i) - A(i,i-1)*ynew(i-1))/A(i,i);
else
z = (b(i) - A(i,i-1)*ynew(i-1) - A(i,i+1)*yold(i+1))/A(i,i);
end
ynew(i) = max(payoffatT(i), yold(i) + omega*(z-yold(i)));
end
if(norm(ynew-yold, 'inf') < tol)
break
else
yold=ynew;
end
end
>```

> [!code]- Code
> ```matlab
>%% Price an American Put Option
>% Implicit Euler - classic pde
>% Black&Scholes Framework
>
>%% 1. Input
>S0=1; K=1; r=0.01; T=1; sigma=0.4;
>M=200; % time
>N=1000; % logprice
>%% 2. Grids
>%Smin=S0*exp((r-sigma^2/2)*T-sigma*6*sqrt(T));
>Smin=0;
>%Smax=S0*exp((r-sigma^2/2)*T+sigma*6*sqrt(T));
>Smax=5;
>S=linspace(Smin,Smax,N+1)';
>dS=(Smax-Smin)/N;  dt=T/M;
>%.% 3. Construction of the matrix
>nodes=S(2:end-1);
>A=-r*nodes/(2*dS)+sigma^2*nodes.^2/(2*dS^2);
>B=-1/dt-sigma^2*nodes.^2/(dS^2)-r;
>C=r*nodes/(2*dS)+sigma^2*nodes.^2/(2*dS^2);
>Matrix=spalloc(N+1,N+1,3*(N-1)+2);
>Matrix(1,1)=1;
>for i=2:N
>    Matrix(i,[i-1 i i+1])=[A(i-1) B(i-1) C(i-1)];
>end
>Matrix(N+1,N+1)=1;
>%% 4. Backward-In-Time Procedure
>% Maturity --> Payoff
>Payoff=max( K-S, 0);
>V=Payoff;
>BC=zeros(size(V));
>for j=M-1:-1:0
>    BC(1)=K-Smin;
>    rhs=-[0;V(2:end-1);0]/dt+BC;
>    V=PSOR(V,Matrix,rhs,Payoff);
>end
>figure
>plot(S,V);
>xlabel('Spot price'); title('Call Price');
>Price=interp1(S,V,S0,'spline')
>```
