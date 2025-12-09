# Deep Reinforcement Learning for Optimal Execution

This project applies Deep Reinforcement Learning (DRL) to the optimal execution problem in financial markets. Specifically, it uses the Proximal Policy Optimization (PPO) algorithm to learn an execution strategy that minimizes the combination of temporary impact costs and inventory risk while accounting for permanent market impact.

## Project Structure

*   `drl_optimal_execution_corrected.py`: The core script implementing the synthetic market simulation using the Heston model. It defines the environment, benchmark agents (TWAP, Almgren-Chriss, Nash Equilibrium), and runs experiments for single-agent and multi-agent scenarios.
*   `drl_optimal_execution_yfinance.py`: An extension of the framework that utilizes real historical market data fetched via `yfinance`. It replaces the Heston simulator with a data handler that replays historical price and volatility sequences.

## Features

*   **Market Simulation:**
    *   **Heston Model:** Simulates asset prices with stochastic volatility, capturing realistic market dynamics like volatility clustering and mean reversion.
    *   **Real Data:** Utilizes historical data (via `yfinance`) to train and evaluate agents on actual market trajectories.
*   **Execution Environment:** A custom OpenAI Gym (Gymnasium) environment that models the optimal execution problem.
    *   **State:** Remaining inventory, time elapsed, and current market volatility.
    *   **Action:** Trading rate (speed of liquidation).
    *   **Reward:** Negative cost (Temporary Impact + Inventory Risk).
*   **Agents:**
    *   **PPO Agent:** A DRL agent trained using Stable Baselines3.
    *   **TWAP Agent:** A Time-Weighted Average Price benchmark that trades at a constant rate.
    *   **Almgren-Chriss:** Computes the theoretical optimal trajectory for a single agent under specific assumptions.
    *   **Nash Equilibrium:** Computes the symmetric optimal trajectory for multiple competing agents.
*   **Multi-Agent Support:** Simulates a competitive environment where the agent's permanent market impact is amplified, mimicking the presence of other identical traders.

## Installation

1.  **Clone the repository.**
2.  **Install dependencies:**
    ```bash
    pip install stable-baselines3[extra] gymnasium tqdm pandas numpy matplotlib yfinance
    ```
    *(Note: The scripts attempt to install these automatically if missing)*

## Usage

### Synthetic Data (Heston Model)

Run the main script to train the agent on synthetic data and generate analysis plots:

```bash
python drl_optimal_execution_corrected.py
```

This will:
1.  Train a PPO agent in a single-agent environment.
2.  Compare the agent's performance against the Almgren-Chriss optimal trajectory.
3.  Simulate a specific volatile day to visualize dynamic adaptation compared to TWAP.
4.  Analyze the distribution of execution costs.
5.  Run a sweep over different numbers of competing agents ($N$) to analyze liquidation behavior in a multi-agent setting.

### Real Data (Yahoo Finance)

To train and evaluate on real market data (e.g., AAPL):

```bash
python drl_optimal_execution_yfinance.py
```

This script allows you to configure:
*   `TICKER`: The stock symbol (e.g., "AAPL", "NVDA").
*   `DATA_INTERVAL`: The data frequency (e.g., "1h").
*   `INITIAL_INVENTORY`: The amount to liquidate.
*   `LAMBDA_RISK`: The risk aversion parameter.

## Key Parameters

*   `ETA_TEMP`: Coefficient for temporary market impact (slippage).
*   `BETA_PERM`: Coefficient for permanent market impact.
*   `LAMBDA_RISK`: Risk aversion parameter penalizing slow liquidation during high volatility.
*   `T_STEPS`: Number of time steps allowed for liquidation.

## Results

The project generates several plots to visualize performance:
*   **Inventory Trajectories:** Comparing DRL, TWAP, and theoretical optima.
*   **Execution Costs:** Histograms of costs to assess risk profiles.
*   **Multi-Agent Dynamics:** Showing how liquidation speed increases with the number of competing agents.

## Disclaimer

This software is for educational and research purposes only. It is not financial advice and should not be used for live trading without extensive testing and risk management.
