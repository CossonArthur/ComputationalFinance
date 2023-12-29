The price of an option depends of the future value of the underlying asset, discounted by the return of the free rate asset:
$$
\begin{align*}
\text{price} &= E[e^{-rT}\Phi(S_T)]\\
\text{where,}\\
r &: \text{rate of risk-free asset}\\
T &: \text{time of execution}\\
\Phi &: \text{pay-off of the option}\\
\end{align*}
$$
*For simplicity reason, only call payoff is shown for the payoff*
$$
\begin{align*}
\text{Call} &: \Phi(S_T) = \max(S_t-K) \\
\text{Put} &: \Phi(S_T) = \max(K-S_t)
\end{align*}
$$

# European

## Definition
A European option is a type of financial derivative that grants the holder the right, but not the obligation, to buy (call option) or sell (put option) an underlying asset at a predetermined price (strike price) on or before the expiration date.
- **Expiration Date:** European options can only be exercised at the expiration date, as opposed to American options, which can be exercised at any time before expiration.
- **Settlement:** Settlement of European options occurs at the expiration date, where the option holder either exercises the option or allows it to expire worthless.
- **Advantages:** European options simplify decision-making for investors, as they eliminate the need for continuous monitoring and decision-making throughout the option's life.

## Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$


> [!code]- Code
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```


>[!notes]- Black & Scholes
>![Black & Scholes](Black%20&%20Scholes.md)


# American 
## Definition
An American option is a type of financial derivative that grants the holder the right, but not the obligation, to buy (call option) or sell (put option) an underlying asset at a predetermined price (strike price) at any time before or on the expiration date.
- **Exercise Flexibility:** Unlike European options, American options provide flexibility, allowing the holder to exercise the option at any point before or on the expiration date.
- **Expiration Date:** Similar to European options, American options have an expiration date, but the key distinction lies in the flexibility of exercising the option prior to this date.
- **Settlement:** Settlement for American options can occur at any time before or on the expiration date, giving the holder the advantage of choosing an optimal exercise time.
- **Advantages:** The flexibility in exercising American options provides enhanced opportunities for risk management and profit realization compared to European options.

## Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$

> [!code]- Code
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
to be modified


# Asian 
## Definition
An Asian option is a type of financial derivative whose payoff is determined by the average price of the underlying asset over a specified period, rather than the spot price at a single point in time.
- **Averaging Period:** The key feature of Asian options is the averaging mechanism, where the payoff is influenced by the average price of the underlying asset over a predetermined period, such as the entire option's life or a specific interval.
- **Floating or Fixed Strike:** Asian options can have either a floating or fixed strike price. For a fixed strike Asian option, the average price is compared to the strike price. In a floating strike Asian option, the payoff is based on the difference between the average price and the spot price.
- **Advantages:** The use of average prices tends to reduce the impact of short-term price fluctuations, making Asian options attractive for investors seeking to hedge against volatility.
## Discounted pay-off
$$
\begin{align}
\text{Fixed Strike} &: \Phi(S_T) = e^{-rT}\cdot \max(\text{mean}(S)-K,0)\\
\text{Floating Strike} &: \Phi(S_T) = e^{-rT}\cdot \max(S_T-\text{mean}(S),0)\\
\end{align}
$$

> [!code]- Code
> Fixed Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( mean(S,2)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>
>Floating Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,end)-mean(S,2), 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```


# Lookback Option

## Definition

A Lookback option is a type of financial derivative that gives the holder the right, but not the obligation, to buy (call option) or sell (put option) the underlying asset at its highest or lowest price over a specified period. The unique feature of a Lookback option is that the payoff depends on the extreme price (maximum or minimum) reached by the underlying asset during the option's life.

**Types:**
-  **Fixed Lookback Option:** Pays the maximum (for a call) or minimum (for a put) price of the underlying asset over the option period.
- **Floating Lookback Option:** Pays the maximum (for a call) or minimum (for a put) price of the underlying asset at the option's expiration.
**Advantages:**
Lookback options provide investors with the opportunity to benefit from favorable price movements without being locked into a single price point.

## Discounted Payoff

The discounted payoff for a Lookback option depends on whether it is a call or a put option. For a call option, the discounted payoff is given by:

$$
\begin{align}
\text{Floating strike} &: \Phi(S_T)=e^{−rT}\cdot(S_T​−\min_{0\leq t\leq T}​S_t​)\\
\text{Fixed strike} &: \Phi(S_T)=e^{−rT}\cdot \max(S_\max​−K)
\end{align}
$$

> [!code]- Code
> Floating strike:
>```matlab
>DiscPayoff_Call = exp(-r*T) * (S(:,end) - min(S, [], 2));
>[Price_Call, ~, Price_CI_Call] = normfit(DiscPayoff_Call);
>```
>
>Fixed strike
>```matlab
>DiscPayoff_Call = exp(-r*T) * (max(S, [], 2) - K);
>[Price_Call, ~, Price_CI_Call] = normfit(DiscPayoff_Call);
>```



# Barrier
## Definition
A barrier option is a type of financial derivative with a feature known as a barrier, which, when crossed, can alter the option's characteristics. Barrier options can be either knock-in or knock-out, depending on whether the barrier activates or deactivates the option.
- **Barrier Types:**
    - **Knock-In:** In a knock-in barrier option, the option becomes active (starts to exist) only if the underlying asset's price reaches or crosses a specified barrier level during the option's life.
    - **Knock-Out:** In a knock-out barrier option, the option becomes null and void if the underlying asset's price reaches or crosses a predetermined barrier level during the option's life.
- **Down-and-In, Up-and-In, Down-and-Out, Up-and-Out:**
    - **Down-and-In (DI):** The option becomes active if the price falls below the barrier.
    - **Up-and-In (UI):** The option becomes active if the price rises above the barrier.
    - **Down-and-Out (DO):** The option becomes null and void if the price falls below the barrier.
    - **Up-and-Out (UO):** The option becomes null and void if the price rises above the barrier.
- Advantages:** Barrier options are often used for risk management, allowing investors to tailor their exposure to specific price movements and market conditions.

## Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$

# Basket
## Definition
A basket option is a type of financial derivative that provides the holder with the right, but not the obligation, to buy or sell a portfolio of underlying assets at a predetermined price (strike price) on or before the expiration date.
- **Multiple Underlying Assets:** Unlike single-asset options, basket options are linked to a basket or portfolio of underlying assets, which can include stocks, commodities, or other financial instruments.
- **Advantages:** Investors use basket options for risk management and to hedge against movements in a diversified portfolio, providing a more comprehensive approach to managing market exposure.
## Discounted pay-off
$$
\Phi(S_T) = e^{-rT}\cdot \max(S_T-K,0)
$$


> [!code]- Code
> Fixed Strike
> ```matlab
> DiscPayoff=exp(-r*T)*max( S(:,2)-K, 0);
>[Price,~,Price_CI]=normfit( DiscPayoff )
>```
>to be mdified
