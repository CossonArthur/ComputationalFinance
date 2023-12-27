
Hypothesis : 
- No dividend paid
- No transaction cost
- Risk free asset known and constant
- Asset return $\sim N(\mu, \sigma)$
- Efficient and random market

$$ 
\begin{align*}
C(S, t) &= S_0 N(d_1) - X e^{-r(T - t)} N(d_2)\\
d_1 &= \frac{\ln\left(\frac{S_0}{X}\right) + \left(r + \frac{\sigma^2}{2}\right)(T - t)}{\sigma \sqrt{T - t}} \\
d_2 &= d_1 - \sigma \sqrt{T - t}
\end{align*}
$$

>[!notes]- Code
>```matlab
>  function bs=fun(vol,spot,strike,rf,maturity)
>    % vol: volatility
>    % spot: spot price
>    % strike: strike price
>    % rf: risk free rate
>    % maturity: maturity
>
>    d1=(log(spot./strike)+(rf+vol.*vol/2).*maturity)./ (vol*sqrt(maturity));
>    d2=d1-vol.*sqrt(maturity);
>
>    nd1=normcdf(d1,0,1); nd2=normcdf(d2,0,1);
>    bs=(spot.*nd1-strike.*exp(-rf.*maturity).*nd2);
>```
