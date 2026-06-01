# Trader Performance vs Bitcoin Market Sentiment

This repository analyzes the relationship between trader performance (Hyperliquid historical trades) and Bitcoin market sentiment (Fear/Greed index). The goal is to uncover patterns that can inform smarter trading strategies.

**Datasets**
- **Historical trader data**: provided via Google Drive — https://drive.google.com/file/d/1IAfLZwu6rJzyWKgBToqwSmmVYU6VbjVs/view?usp=sharing
- **Fear/Greed Index**: provided via Google Drive — https://drive.google.com/file/d/1PgQC0tO8XN-wqkNyghWc_-mnrYv_nhSf/view?usp=sharing

**Notebook**
- Analysis notebook: [bitcoin_sentiment_trader_analysis.ipynb](bitcoin_sentiment_trader_analysis.ipynb)

**What I added / implemented**
- Drive download: the notebook will download the CSVs automatically from the provided Drive IDs using `gdown` if local files are missing.
- OpenAI summaries: hidden prompt for the API key via `getpass()` (or set `OPENAI_API_KEY` env var). The notebook uses a robust client fallback to generate AI summaries; if the key is not provided the notebook falls back to a data-driven summary.
- Simple ML + explainability: a RandomForest classifier to predict per-trade profitability (`label_win`) and SHAP `TreeExplainer` summary plots. An optional regressor predicts PnL magnitude.

**Requirements / How to run**
- Recommended Python packages (install before running):
```
pip install pandas numpy matplotlib seaborn scikit-learn shap gdown openai
```
- OpenAI key: set the env var or provide at the prompt when the notebook asks:
```
# PowerShell (current session)
$Env:OPENAI_API_KEY="sk-..."
# Persist across sessions
setx OPENAI_API_KEY "sk-..."
```
- Open the notebook and run cells in order. The Drive downloads and dependency installs are handled inline where needed.

**Current limitations / missing basic fields**
- `leverage` and `event` are not present in the supplied `historical_data.csv` and cannot be analyzed unless added or proxied.
- Additional preprocessing recommended: unify `size` columns, normalize coin symbols, align timestamps to UTC, compute net PnL (pnl − fee), and create lagged sentiment features.
- No `requirements.txt` yet; installs are inline in the notebook.

**Suggested next steps**
- Add `requirements.txt` and/or a lightweight environment file.
- Compute risk metrics per regime (Sharpe, Sortino, CVaR) and holding-period statistics.
- Add lagged sentiment features and retrain models with cross-validation and class-imbalance handling.
- Build an interactive dashboard (Streamlit) to explore account-level performance by sentiment regime.

If you want, I can add a `requirements.txt`, infer proxies for `leverage`/`event` where possible, or extend the ML pipeline with CV and lagged sentiment features. Which would you like next?

## Architecture

High-level components and data flow:

- **Data Sources**: Google Drive CSVs (Historical trades, Fear/Greed index) — downloaded by the notebook when missing.
- **ETL / Preprocessing** (notebook): cleaning, timestamp normalization, numeric coercion, and merging trades with daily sentiment by `date`.
- **Feature Engineering**: trade-level features (notional, fee, side), sentiment features (value, classification), one-hot coin encoding, and (optional) lagged/rolling sentiment.
- **Analysis & Risk**: aggregated regime metrics, win-rate, net PnL, and downstream risk metrics (Sharpe/Sortino/CVaR) — extendable in the notebook.
- **Modeling**: simple ML pipeline included (RandomForest classifier for win/loss, optional regressor for PnL), with SHAP explainability for feature attribution.
- **AI Summary**: OpenAI-based concise conclusions (uses `OPENAI_API_KEY` from env or notebook prompt) with a data-driven fallback.
- **Outputs**: plots, SHAP visuals, CSV exports, and recommendations printed in the notebook.

## Workflow

Typical step-by-step workflow to reproduce analysis:

1. Ensure Python environment and install dependencies (recommended):
```
pip install pandas numpy matplotlib seaborn scikit-learn shap gdown openai
```
2. Provide your OpenAI API key either by setting `OPENAI_API_KEY` in the environment or entering it when prompted by the notebook.
3. Open and run `bitcoin_sentiment_trader_analysis.ipynb` from top to bottom. The notebook will:
	- download the datasets from Google Drive if local files are missing,
	- clean and normalize both datasets,
	- merge trades to daily sentiment regimes,
	- compute helper columns and basic aggregated metrics,
	- run a simple ML classifier and show SHAP explanations,
	- attempt an AI-generated summary using the provided key, falling back to a data-driven summary if the key is not available.
4. Inspect outputs: plots, `analysis_df` tables, model metrics, SHAP plots, and the AI summary.
5. (Optional) Extend the notebook: add lagged sentiment, account-cohort analysis, risk metrics, and dashboarding.
