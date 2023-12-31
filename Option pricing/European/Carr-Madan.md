For stochastic process with jumps, there is not general formula to compute the option price, as the characteristic function of the underlying asset is not known. To compute the call option price  $C(k) = e^{−rT} E[(e^{rT+X_T} − e^k)^+]$, we use the Fourier Transform.

$$
\begin{align*}
C(K) &= \max(1 - e^{K-rT},0) - \frac{1}{2\pi} \int_{-\infty}^{\infty} \mathrm{Re}\left( \frac{e^{-iuK}\phi_T(u-i)}{iu\phi(-i)} \right) K^{-iu} du \\
\text{where:} \\
& C(K) \text{ is the call option price}, \\
& K \text{ is the option strike price}, \\
& \phi(u) \text{ is the characteristic function}.
\end{align*}
$$

