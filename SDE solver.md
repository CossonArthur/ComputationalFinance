To solve the SDE, $dX_t=a(X_t,t) \cdot dt+b(X_t,t) \cdot dW_t$ 

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