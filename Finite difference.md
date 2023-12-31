**M >> N**
With M the discretisation in time, and N discretisation in strike

# PDE
## B&S
$$
\begin{align}
&x = \ln(\frac{S}{S_0})\\
&\frac{∂V}{∂t}​+(r-\frac{σ^2}2)\frac{∂V}{∂x}​​+ \frac{σ^2}2 \frac{∂^2V}{∂x^2}​​−rV=0\\
&V = V(t,x)\\
&V(T,x) = \max(S_0e^x-K,0)
\end{align}
$$


# Difference method
To solve the SDE, $dX_t=a(X_t,t) \cdot dt+b(X_t,t) \cdot dW_t$ 

For all the schema, $dW_t$ when discretise is $\sqrt{\Delta t} . N(0,1)$

# Euler
## Explicit
$$
X_{n+1} = X_n + dX_{n}
$$
## Implicit

$$
X_{n+1} = X_n + dX_{n+1}
$$
# Theta
Theta method is a generalisation of Euler, $\theta=0$ <=> Explicit and  $\theta=1$ <=> Implicit 
$$
X_{n+1} = X_n + (1-\theta) \cdot dX_{n} + \theta \cdot dX_{n+1}
$$

# Milstein
$$
X_{n+1} = X_n + dX_n + \frac{1}{2} b(X_n, t_n) \frac{\partial b}{\partial x}(X_n, t_n) \left(\Delta W_n^2 - \Delta t\right)
$$