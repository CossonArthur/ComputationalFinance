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

Params :
- $\lambda_+$ , $\lambda_-$, $\sigma$, $\mu_J$ $\sigma_J$
$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + \lambda \left(\exp\left(-\frac{\sigma_J^2 u^2}{2} + i\mu_J u\right) - 1\right)
$$


>[!notes]- Code
>```matlab
>  Ndt 
>```




# Kou

Params :
- $\lambda_+$ , $\lambda_-$, $\sigma$, $p$

$$
\Psi(u) = -\frac{\sigma^2 u^2}{2} + iu\lambda\left(\frac{p}{\lambda_+ - iu} - \frac{1-p}{\lambda_- + iu}\right)
$$
