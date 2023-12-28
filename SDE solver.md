To solve the SDE, $dX_t=a(X_t,t) \cdot dt+b(X_t,t) \cdot dW_t$ 

# Euler
## Explicit

## Implicit

# Theta

# Milstein
$$
X_{n+1} = X_n + a(X_n, t_n) \Delta t + b(X_n, t_n) \Delta W_n + \frac{1}{2} b(X_n, t_n) \frac{\partial b}{\partial x}(X_n, t_n) \left(\Delta W_n^2 - \Delta t\right)
$$