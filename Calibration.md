# Black & scholes 
optimise the Least Square difference between the model and the real value of the option



The models parameters need to be calibrated to reflect the reality of the market 

> [!code] Code
>```matlab
>x0=[0.4, 2, 0.5, 5, 5]; % init values
>LB=[0.1, 0, 0, 1,1]; % lower bounds for params
>UB=[0.8,20,1 , 50,50];  % upper bounds for params
>
>[params,error]=lsqnonlin(@(params) fun(params,spot,strike,r,maturity,mktp),x0,LB,UB)
>```


