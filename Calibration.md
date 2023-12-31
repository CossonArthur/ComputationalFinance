# Black & scholes 
optimise the Least Square difference between the model and the real value of the option



The models parameters need to be calibrated to reflect the reality of the market 
```matlab
[params,error]=lsqnonlin(@(params) fun(params,spot,strike,r,maturity,mktp),x0,LB,UB)
```


