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
>  [Call,Put] = blsprice(S0,K,r,T,sigma)
>```
