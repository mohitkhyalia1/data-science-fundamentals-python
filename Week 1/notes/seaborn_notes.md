# Seaborn — Quick Reference Notes
**Analytics Club | Learners' Space 2026**

---

## Setup

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Always set a theme first
sns.set_theme(style='whitegrid', palette='Set2', font_scale=1.1)

# Most seaborn functions return an Axes — so you can still use matplotlib on top
fig, ax = plt.subplots(figsize=(8, 5))
sns.someplot(..., ax=ax)
ax.set_title('Custom Title')   # matplotlib call on the same axes
plt.tight_layout()
plt.show()
```

---

## Themes

```python
sns.set_theme(style='whitegrid')   # my default — clean, readable
# Other options:
# 'darkgrid'  — dark background with grid
# 'dark'      — dark background, no grid
# 'white'     — white, no grid
# 'ticks'     — white with ticks, no grid

sns.set_context('talk')    # scale everything for presentations
# 'paper', 'notebook', 'talk', 'poster'

sns.reset_defaults()       # go back to matplotlib defaults
```

---

## Color Palettes

```python
sns.set_palette('Set2')         # set globally

# Qualitative (for categories)
'Set1', 'Set2', 'Set3'
'tab10', 'tab20'
'pastel', 'muted', 'deep', 'bright', 'colorblind'

# Sequential (for ordered data, low → high)
'Blues', 'Greens', 'Oranges', 'Purples'
'Blues_d'  # _d suffix = darker version

# Diverging (for data with a meaningful midpoint — correlations, etc.)
'coolwarm', 'RdBu', 'PiYG', 'BrBG'

# Preview a palette
sns.palplot(sns.color_palette('Set2', 8))

# Custom
custom = ['#4C72B0', '#DD8452', '#55A868', '#C44E52']
sns.set_palette(custom)
```

---

## Distribution Plots

```python
# Histogram + optional KDE
sns.histplot(data=df, x='col', bins=30, kde=True, color='steelblue')
sns.histplot(data=df, x='col', hue='category', multiple='stack')

# KDE only (smooth density curve)
sns.kdeplot(data=df, x='col', fill=True, alpha=0.5)
sns.kdeplot(data=df, x='col', hue='category')

# ECDF (empirical cumulative distribution)
sns.ecdfplot(data=df, x='col')

# Rug plot (tick marks at each data point)
sns.rugplot(data=df, x='col')
```

---

## Categorical Plots

### Strip / Swarm (show all data points)
```python
sns.stripplot(data=df, x='category', y='value', jitter=True, alpha=0.6)
sns.swarmplot(data=df, x='category', y='value')  # no overlaps
```

### Box / Violin
```python
sns.boxplot(data=df, x='category', y='value', hue='gender', palette='Set2')
sns.violinplot(data=df, x='category', y='value', inner='quartile')
# inner= 'box', 'quartile', 'point', 'stick', None

# Combine — box inside violin
sns.violinplot(data=df, x='cat', y='val', inner='box')
```

### Bar / Count
```python
# barplot — mean (or other statistic) per category, with error bars
sns.barplot(data=df, x='category', y='value',
            estimator='mean',    # or np.median, np.sum, ...
            errorbar='sd',       # 'sd', 'se', 'ci', None
            palette='Blues_d')

# countplot — frequency of each category value
sns.countplot(data=df, x='category', hue='gender', palette='Set1')

# pointplot — means connected by lines (good for interaction effects)
sns.pointplot(data=df, x='time', y='score', hue='group', markers='o')
```

---

## Relational Plots

```python
# Scatter plot
sns.scatterplot(data=df, x='col1', y='col2',
                hue='category',    # color
                style='group',     # marker shape
                size='magnitude',  # marker size
                sizes=(40, 200),   # size range
                alpha=0.8,
                palette='Set1')

# Line plot (with confidence interval)
sns.lineplot(data=df, x='x', y='y', hue='group', errorbar='ci')
```

---

## Regression Plots

```python
# Scatter + regression line
sns.regplot(data=df, x='col1', y='col2', color='steelblue',
            scatter_kws={'alpha': 0.5}, line_kws={'color': 'red'})

# Same but with hue grouping (uses lmplot — returns FacetGrid not Axes)
sns.lmplot(data=df, x='col1', y='col2', hue='category')
```

---

## Matrix Plots

```python
# Heatmap
sns.heatmap(data,
            annot=True,       # print values in cells
            fmt='.2f',        # format string for annotations
            cmap='coolwarm',  # colormap
            vmin=-1, vmax=1,  # fix color scale
            linewidths=0.5,   # cell borders
            mask=mask)        # mask certain cells (e.g. upper triangle)

# Correlation matrix heatmap — full example
corr = df.corr()
mask = np.triu(np.ones_like(corr, dtype=bool))   # mask upper triangle
sns.heatmap(corr, annot=True, fmt='.2f', cmap='coolwarm', mask=mask)

# Clustermap — heatmap with hierarchical clustering
sns.clustermap(data, cmap='viridis', standard_scale=1)
```

---

## Multi-Plot Grids

```python
# pairplot — scatter for all pairs, distribution on diagonal
sns.pairplot(df, hue='category', diag_kind='kde',
             plot_kws={'alpha': 0.6})

# FacetGrid — repeat a plot for each category
g = sns.FacetGrid(df, col='category', row='gender', height=4)
g.map(sns.histplot, 'value', bins=20)
g.add_legend()

# PairGrid — more control than pairplot
g = sns.PairGrid(df[['a','b','c']])
g.map_diag(sns.histplot)
g.map_upper(sns.scatterplot)
g.map_lower(sns.kdeplot)
```

---

## Built-in Datasets (for practice)

```python
# List all
sns.get_dataset_names()

# Load any
tips     = sns.load_dataset('tips')       # restaurant tips
iris     = sns.load_dataset('iris')       # flower measurements
penguins = sns.load_dataset('penguins')   # penguin species
titanic  = sns.load_dataset('titanic')    # survival data
diamonds = sns.load_dataset('diamonds')   # diamond prices
flights  = sns.load_dataset('flights')    # monthly passengers
```

---

## Seaborn + Matplotlib Together

```python
# Pattern: create axes with matplotlib, pass to seaborn
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

sns.boxplot(data=df, x='day', y='total_bill', ax=axes[0])
axes[0].set_title('Box Plot')

sns.violinplot(data=df, x='day', y='total_bill', ax=axes[1])
axes[1].set_title('Violin Plot')

plt.tight_layout()
plt.show()

# lmplot / pairplot return FacetGrid — access the figure like this:
g = sns.pairplot(df)
g.fig.suptitle('Title', y=1.02)
g.fig.savefig('pairplot.png', bbox_inches='tight')
```

---

## Seaborn vs Matplotlib Cheat Sheet

| Task | Seaborn | Matplotlib |
|------|---------|-----------|
| Quick distribution | `histplot`, `kdeplot` | `ax.hist()` |
| Scatter with groups | `scatterplot(hue=)` | Multiple `ax.scatter()` calls |
| Mean per category | `barplot` | Manual groupby + `ax.bar()` |
| Correlations | `heatmap(df.corr())` | `ax.imshow()` (ugly) |
| All-pairs overview | `pairplot` | No equivalent |
| Custom layout | Use `ax=` parameter | Native |
| Fine annotation control | Limited | Full |
| Animations | No | Yes (`FuncAnimation`) |

---

## Things That Caught Me Out

| Issue | Fix |
|-------|-----|
| `lmplot` / `pairplot` don't take `ax=` | They return a `FacetGrid` — access `.fig` and `.axes` |
| Palette warning without `hue` | Assign the categorical variable to `hue` and set `legend=False` |
| Plot doesn't show | Call `plt.show()` after (in scripts) |
| Seaborn style bleeds into other plots | Use `with sns.axes_style('whitegrid'):` as context manager |
| `barplot` changed in v0.13 | `ci=` replaced by `errorbar=`, `estimator` accepts strings |

---

*Last updated: Phase 1, Learners' Space 2026*
