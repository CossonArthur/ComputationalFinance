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


# PIDE