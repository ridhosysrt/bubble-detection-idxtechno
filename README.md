# Bubble Detection in Indonesia's Technology Sector Index (IDXTECHNO)

## Background

**Topic:** Detecting cyclical boom-and-bust episodes (speculative price bubbles) in Indonesia's Technology Sector Index (IDXTECHNO) and analyzing how macroeconomic variables influence their occurrence.

**Why it matters:** The technology sector plays a pivotal role in capital market stability, and its dynamics are evolving quickly. Understanding when and why explosive price behavior emerges helps regulators and investors manage speculative risk.

**Period:** January 25, 2021 – August 29, 2025 (daily time series)

**Approach:**
1. Detect periods of price explosiveness using the **Generalized Supremum Augmented Dickey-Fuller (GSADF)** technique (Phillips & Yu, 2011; Phillips, Shi & Yu, 2015).
2. Examine the influence of macroeconomic variables on bubble occurrence using **Firth's penalized logistic regression**.

**Variables:**

| Variable | Description |
|---|---|
| IDXTECHNO | Technology Sector Index (daily closing price) |
| M2 | Broad money |
| Interest rate | Benchmark interest rate |
| WUI | World Uncertainty Index |

## Research Questions

1. Is there a stock price bubble in Indonesia's technology sector between January 2021 and August 2025?
2. How does broad money (M2) affect the probability of a price bubble in the technology sector?
3. How do interest rates affect the probability of a price bubble in the technology sector?
4. How does global uncertainty (WUI) affect the probability of a price bubble in the technology sector?

## Methodology

### 1. Bubble detection (GSADF / BSADF)

A right-tailed ADF test is run over a backward-expanding sample window, and the backward supremum ADF (BSADF) statistic is compared with a critical value.

| Parameter | Value |
|---|---|
| Minimum window size (`r0`) | 70 observations |
| ADF lags | 2 |
| Critical value (95%, right-tailed) | 1.49 (Phillips et al., 2015) |
| Series used | Log of daily index price |

**Bubble period definition:** a bubble period is recorded only when explosiveness is detected for **more than 3 days**.

### 2. Regression analysis

Firth's penalized logistic regression is used to estimate the effect of M2, interest rates, and WUI on the probability that a given day falls within a bubble period. The penalized approach is suitable here because bubble days are rare events.

## Results

### Detected bubble periods

Three distinct periods of explosive price behavior were identified:

| # | Period | Days detected | Context |
|---|---|---|---|
| 1 | 3 Jun 2021 – 16 Jun 2021 | 7 | COVID-19 pandemic digitization wave |
| 2 | 5 Dec 2022 – 21 Dec 2022 | 13 | Global "tech winter" correction |
| 3 | 21 Feb 2025 – 14 Mar 2025 | 13 | Recent surge in Artificial Intelligence (AI) adoption |

### Macroeconomic determinants

| Variable | Effect on bubble probability |
|---|---|
| Broad money (M2) | **Positive and significant** |
| Interest rate | Not significant |
| World Uncertainty Index (WUI) | Not significant |

### Policy implication

Only broad money (M2) shows a significant positive effect, which suggests that **market liquidity management** should be prioritized to mitigate speculative risk and support sustainable growth in the technology sector.

## Sample Code

The snippet below shows the core BSADF loop used for bubble detection.

```python
import pandas as pd
import numpy as np
import statsmodels.api as sm
import matplotlib.pyplot as plt

# Parameters
r0 = 70          # minimum window size
adf_lags = 2     # lags for the ADF test
crit = 1.49      # 95% right-tailed critical value (Phillips et al., 2015)

# log_prices: numpy array of log(IDXTECHNO)
delta_log_prices = log_prices[1:] - log_prices[:-1]
n = len(delta_log_prices)
BSADF = np.array([])

for r2 in range(r0, n):
    ADFS = np.array([])
    for r1 in range(0, r2 - r0 + 1):
        X = pd.DataFrame()
        X[0] = log_prices[r1:r2 + 1]
        for j in range(1, adf_lags + 1):
            X[j] = np.append(np.zeros(j), delta_log_prices[r1:r2 + 1 - j])
        y = delta_log_prices[r1:r2 + 1]
        res = sm.OLS(y, sm.add_constant(np.array(X))).fit()
        ADFS = np.append(ADFS, res.params[1] / res.bse[1])
    BSADF = np.append(BSADF, max(ADFS))

# Dates where BSADF exceeds the critical value
print(prices.index[r0 + 1:][BSADF > crit])
```

## Getting Started

```bash
git clone https://github.com/<your-username>/bubble-detection-idxtechno.git
cd bubble-detection-idxtechno
pip install pandas numpy statsmodels matplotlib openpyxl
```

Place the dataset (Excel file, sheet `HARIAN`) in the project folder, update the `excel_file` path in the notebook, and run all cells.

## Tech Stack

Python · pandas · NumPy · statsmodels · Matplotlib

## References

- Phillips, P. C. B., & Yu, J. (2011). Dating the timeline of financial bubbles during the subprime crisis. *Quantitative Economics*, 2(3), 455–491.
- Phillips, P. C. B., Shi, S., & Yu, J. (2015). Testing for multiple bubbles: Historical episodes of exuberance and collapse in the S&P 500. *International Economic Review*, 56(4), 1043–1078.
- Firth, D. (1993). Bias reduction of maximum likelihood estimates. *Biometrika*, 80(1), 27–38.

## Author

**<Your Name>** – <your email or LinkedIn>

## License

<Add license, e.g. MIT>
