# Kryptotron worker

Kryptotron is Ocean's long-running Binance worker. It evaluates EMA50/EMA200 Golden Cross signals on closed 4-hour candles for BTC/USDC and ETH/USDC, applies shared risk limits and publishes a compact operating state for the Ocean dashboard.

## Safety properties

- buy intents are persisted before submission;
- Binance client order IDs stay stable across retries;
- ambiguous orders are reconciled instead of assumed to have failed;
- every filled position receives exchange-native OCO protection;
- pending protection is recovered after a restart;
- pausing automation blocks new entries but keeps existing protection active;
- daily and weekly loss limits, trade limits and cooldowns are enforced;
- state, trades and summary markers survive worker restarts.

Exchange-native protection is the critical boundary: a worker outage must not remove the protective order already held by Binance.

## Configuration

Create an isolated virtual environment and install dependencies:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Use a Binance testnet key while developing. For mainnet, disable withdrawals and restrict the key to the worker's fixed egress IP. Keep Binance, Supabase and Telegram credentials outside Git.

## Tests

```bash
python -m unittest discover -s tests -v
```

The suite covers restart-safe order intents, OCO construction and reconciliation, exchange filters, event retention and Prague-time daily/weekly scheduling.

## Limitations

This worker currently represents one bot instance and one exchange connection. The strategy implementation is not evidence of profitability. Production use requires independent strategy validation, paper trading, monitoring and a reviewed multi-tenant data model.
