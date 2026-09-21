# Novaretail-correlation-analysis
Correlation analysis of 15,000 ecommerce customers in Python — Pearson, Spearman, point-biserial and Cramér's V

# Behavioural Drivers of Annual Revenue — NovaRetail+ (Python)

Correlational analysis of 15,000 ecommerce customers, identifying which behaviours are associated with the annual revenue each customer generates — and which are not.

## Business Objective

NovaRetail+ is a Latin American ecommerce platform. Closing out 2024, its Growth and Retention team needed an answer to one question:

> **Which customer behaviours are most strongly associated with the annual revenue a customer generates?**

The analysis maps associations rather than causes, and reports which of them are large enough to act on.

## Dataset

`novaretail_comportamiento_clientes_2024.csv` — 15,000 customers, 12 columns, no nulls, no duplicates.

| Column | Type | Description |
|---|---|---|
| `id_cliente` | ID | Unique customer identifier |
| `edad` | Numeric | Customer age |
| `nivel_ingreso` | Numeric | Customer's own estimated annual income |
| `visitas_mes` | Numeric | Visits to the app or site during the month |
| `compras_mes` | Numeric | Purchases made during the month |
| `gasto_publicidad_dirigida` | Numeric | Ad spend allocated to that user |
| `satisfaccion` | Numeric | Satisfaction score, 1 to 5 |
| `miembro_premium` | Binary | 1 if premium subscriber |
| `abandono` | Binary | 1 if the customer churned |
| `tipo_dispositivo` | Categorical | móvil, escritorio, tablet |
| `region` | Categorical | norte, sur, oeste, este |
| `ingreso_anual` | Numeric | **Annual revenue generated for the company** — the target |

> The dataset belongs to TripleTen and is not redistributed here. The notebook stores its outputs, so every result is visible without running it.

## Method

The coefficient is chosen by variable type, never by which one returns a larger number:

| Pairing | Coefficient | Used for |
|---|---|---|
| Numeric × numeric | Pearson + Spearman | `compras_mes`, `visitas_mes` against revenue |
| Numeric × binary | Point-biserial | `miembro_premium`, `abandono` against revenue |
| Categorical × categorical | Cramér's V | churn against device, region and premium status |

Pearson and Spearman are reported side by side as a stability check: when they agree, the relationship holds whether measured by magnitude or by rank order.

## Key Findings

### 1. Purchases and revenue are the same signal, not two

**Pearson 0.967, Spearman 0.967** — the strongest value in the matrix and far past the 0.85 collinearity threshold.

Monthly purchases and annual revenue are close to two measurements of the same thing rather than two related behaviours. Any model or dashboard using both double-counts the same information.

### 2. Visits move with revenue, but weakly

**Pearson 0.337, Spearman 0.321.**

After purchases, visit frequency is the behaviour most associated with revenue — but the relationship explains only a small share of the variation. Ad spend correlates with visits at 0.58, more strongly than visits correlate with revenue. There is a plausible chain from spend to visits to revenue, but it weakens at each step and only an experiment can confirm it holds end to end.

### 3. Thirty percent of customers generate no revenue at all

4,567 customers (30.4%) produced zero revenue, and they are **exactly** the 4,567 who made zero purchases — the two sets match perfectly. This is not missing data or a recording error: revenue is zero because no purchase happened.

It also explains part of Finding 1. Excluding non-buyers, the purchases-revenue correlation drops from **0.967 to 0.934**. A coefficient computed over a population containing a large, perfectly aligned zero segment reports partly on the existence of that segment rather than on the relationship among the rest.

The largest revenue opportunity in this dataset is not making buyers buy more — it is that a third of the base buys nothing.

### 4. Nothing in this dataset explains churn

| Association with `abandono` | Coefficient |
|---|---|
| Annual revenue (point-biserial) | −0.003 (p = 0.73) |
| Device type (Cramér's V) | 0.007 |
| Region (Cramér's V) | 0.015 |
| Premium status (Cramér's V) | 0.120 |

High-value customers churn at about the same rate as low-value ones. Premium status is the only weak lead. Churn is driven by variables not present here — support contacts, recency, in-session behaviour.

### 5. Who the customer is tells you nothing; what they do tells you something

`edad`, `nivel_ingreso` and `satisfaccion` all sit near zero against revenue. Demographics do not separate valuable customers from the rest in this dataset — behaviour does.

A caveat worth keeping: an aggregate near zero can hide opposing effects between subgroups, and ruling that out would need segmented analysis.

## Notebook

[`novaretail_correlation_analysis.ipynb`](novaretail_correlation_analysis.ipynb) — full analysis in six sections: validation, preparation and assumptions, visual exploration, coefficients, business findings, limitations and next steps.

## Skills Demonstrated

- Selecting a correlation coefficient by variable type (Pearson, Spearman, point-biserial, Cramér's V)
- Implementing Cramér's V from the chi-square statistic
- Detecting and interpreting collinearity above a 0.85 threshold
- Separating statistical significance from practical relevance on n = 15,000
- Diagnosing how a zero-inflated segment inflates a correlation
- Correlation heatmaps and density-aware scatterplots with `alpha`
- Writing non-causal conclusions with explicit limitations

## How to Run

```bash
pip install pandas numpy matplotlib seaborn scipy
jupyter notebook novaretail_correlation_analysis.ipynb
```

The notebook looks for the dataset at `/datasets/novaretail_comportamiento_clientes_2024.csv` and falls back to `data/novaretail_comportamiento_clientes_2024.csv`. Since the dataset is not included, the saved outputs are the reference.

## Tools

- Python 3
- pandas, numpy
- matplotlib, seaborn
- scipy.stats

## Author

**Sebastian Ladino Novoa** — Data Analytics Portfolio
