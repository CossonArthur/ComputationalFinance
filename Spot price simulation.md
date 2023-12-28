# Merton 
Merton model the spot price as following the SDE:
$$
\begin{align*}
dX_t &= \mu_1 X_tdt + \sigma X_tdW_t + \sum^{N(t)}_{i=1} Yi \\
\text{where,}\\
Y_i &\sim N(\mu_2, \delta) \\
N(t) &\sim Poisson(\lambda*t)\\
\end{align*}
$$


>[!notes]- Code
>```matlab
>  Ndt 
>```




# Kou

$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + iu\lambda\left(\frac{p}{\lambda_p - iu} - \frac{1-p}{\lambda_m + iu}\right)
$$
