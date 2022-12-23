# Towards a fully RL-based Market Simulator
## Abstract

We present a new financial framework where two families of RL-based agents representing the Liquidity Providers and Liquidity Takers learn simultaneously to satisfy their objective.

Thanks to a parametrized reward formulation and the use of Deep RL, each group learns a shared policy able to generalize and interpolate over a wide range of behaviors.

This is a step towards a fully RL-based market simulator replicating complex market conditions particularly suited to study the dynamics of the financial market under various scenarios.

## Multi-Agent Framework
#### Configuration and Training
To conduct our research we consider the dealer market as a Partially-Observable Stochastic Game defined in [15]; where each agent has only a partial view of the state of the world and aims at maximizing its own reward function by interacting with its environment.

The different families of agents in our system are LP, LT and ECN.

At each timestep, the LP updates the prices that it streams to the LT according to its observation of the market. Subsequently, and based on this new observation, the LT decides whether or not to trade with the LP or the ECN. The LP can also choose to hedge its position by trading with the ECN.

The problem has thus been designed as a Stackelberg game, where the LT reacts to the action that the LP takes (setting the prices it is willing to trade at).

