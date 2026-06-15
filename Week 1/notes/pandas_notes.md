# Pandas — Quick Reference Notes
**Analytics Club | Learners' Space 2026**

---

## Core Data Structures

```python
import pandas as pd

# Series — 1D labelled array
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
s['b']          # access by label
s[1]            # access by position
s.values        # underlying NumPy array
s.index         # index object

# DataFrame — 2D table
df = pd.DataFrame({'A': [1,2,3], 'B': [4,5,6]})
df.columns      # column names
df.index        # row index
df.values       # NumPy array of all values
df.dtypes       # dtype of each column
```

## Creating DataFrames

```python
# From dict (most common)
pd.DataFrame({'col1': [1,2], 'col2': [3,4]})

# From list of dicts
pd.DataFrame([{'a':1,'b':2}, {'a':3,'b':4}])

# From NumPy array
pd.DataFrame(np.zeros((3,3)), columns=['x','y','z'])

# From CSV / Excel / JSON
pd.read_csv('file.csv')
pd.read_csv('file.csv', sep=';', skiprows=2,
            usecols=['A','B'], nrows=100, index_col=0)
pd.read_excel('file.xlsx', sheet_name='Sheet1')
pd.read_json('file.json')
```

## First Look at a Dataset

```python
df.head(n)         # first n rows (default 5)
df.tail(n)         # last n rows
df.shape           # (rows, cols)
df.columns         # column list
df.dtypes          # data type of each column
df.info()          # non-null counts + dtypes
df.describe()      # stats for numeric columns
df.nunique()       # unique values per column
df.value_counts()  # frequency of each value (Series)
df['col'].unique() # unique values in a column
```

## Selecting Data

```python
df['col']               # single column → Series
df[['col1', 'col2']]    # multiple columns → DataFrame

# loc — label-based
df.loc[3]                       # row with label 3
df.loc[1:4]                     # rows labels 1 to 4 (inclusive)
df.loc[1:4, ['A', 'B']]         # rows 1-4, columns A and B
df.loc[df['A'] > 2, 'B']        # filtered rows, column B

# iloc — position-based
df.iloc[0]           # first row
df.iloc[:3]          # first 3 rows
df.iloc[1:4, 0:2]    # rows 1-3, cols 0-1
df.iloc[-1, -1]      # last row, last column
```

## Filtering Rows

```python
df[df['col'] > 50]
df[(df['A'] > 2) & (df['B'] < 8)]     # AND
df[(df['A'] == 1) | (df['B'] == 5)]   # OR
df[~(df['A'] == 1)]                    # NOT
df[df['col'].isin(['x', 'y', 'z'])]   # membership
df[df['col'].str.contains('pattern')]  # string match

# query() — cleaner for complex filters
df.query('A > 2 and B < 8')
df.query('col in @my_list')   # reference Python variable with @
```

## Adding / Modifying Columns

```python
df['new'] = df['A'] + df['B']           # arithmetic
df['label'] = df['score'].apply(lambda x: 'Pass' if x>=40 else 'Fail')
df['new'] = np.where(df['A'] > 3, 'high', 'low')  # vectorised if-else
df.rename(columns={'old': 'new'}, inplace=True)
df.drop(columns=['col1', 'col2'], inplace=True)
df.drop(index=[0, 2], inplace=True)
```

## Handling Missing Values

```python
df.isnull().sum()                   # count NaNs per column
df.isnull().any(axis=1)             # rows that have any NaN
df.notnull()                        # opposite mask

df.dropna()                         # drop rows with any NaN
df.dropna(axis=1)                   # drop columns with any NaN
df.dropna(thresh=3)                 # keep rows with at least 3 non-NaN

df['col'].fillna(0)                 # fill with 0
df['col'].fillna(df['col'].mean())  # fill with mean
df['col'].fillna(method='ffill')    # forward fill
df.fillna(df.mean(numeric_only=True))  # fill all numerics with column mean

df.duplicated().sum()               # count duplicate rows
df.drop_duplicates(inplace=True)    # remove duplicates
```

## Sorting

```python
df.sort_values('col')                          # ascending
df.sort_values('col', ascending=False)         # descending
df.sort_values(['col1', 'col2'], ascending=[True, False])
df.sort_index()                                # sort by row index
df.rank()                                      # rank each value
df['col'].rank(ascending=False)               # rank column descending
```

## GroupBy

```python
# Basic
df.groupby('col')['value'].mean()
df.groupby('col')['value'].sum()
df.groupby('col')['value'].agg(['mean', 'min', 'max', 'count'])

# Multiple grouping keys
df.groupby(['col1', 'col2'])['value'].mean()

# Named aggregations (pandas ≥ 0.25)
df.groupby('col').agg(
    avg_val=('value', 'mean'),
    total=('value', 'sum'),
    count=('value', 'count')
)

# pivot_table — like Excel pivot
df.pivot_table(values='sales', index='region',
               columns='product', aggfunc='sum', fill_value=0)

# unstack — convert innermost index level to columns
df.groupby(['A','B'])['C'].sum().unstack(fill_value=0)
```

## Merging and Joining

```python
# merge (like SQL JOIN)
pd.merge(left, right, on='key', how='inner')   # only matching rows
pd.merge(left, right, on='key', how='left')    # all left rows
pd.merge(left, right, on='key', how='right')   # all right rows
pd.merge(left, right, on='key', how='outer')   # all rows

# merge on different column names
pd.merge(left, right, left_on='id', right_on='student_id')

# concat — stack DataFrames
pd.concat([df1, df2], axis=0)           # stack rows
pd.concat([df1, df2], axis=1)           # stack columns
pd.concat([df1, df2], ignore_index=True) # reset index after stacking
```

## String Operations (`.str` accessor)

```python
df['col'].str.upper()
df['col'].str.lower()
df['col'].str.strip()                  # remove leading/trailing spaces
df['col'].str.contains('pattern')      # boolean mask
df['col'].str.startswith('prefix')
df['col'].str.replace('old', 'new')
df['col'].str.split(',', expand=True)  # split into multiple columns
df['col'].str.len()                    # length of each string
df['col'].str.extract(r'(\d+)')        # regex extraction
```

## DateTime Operations (`.dt` accessor)

```python
df['date'] = pd.to_datetime(df['date'])   # parse to datetime
df['date'].dt.year
df['date'].dt.month
df['date'].dt.day
df['date'].dt.day_name()                  # 'Monday', 'Tuesday', ...
df['date'].dt.strftime('%Y-%m-%d')        # format as string
```

## Apply and Map

```python
df['col'].apply(func)          # apply function element-wise to a Series
df.apply(func, axis=0)         # apply function column-wise
df.apply(func, axis=1)         # apply function row-wise
df['col'].map({'a': 1, 'b': 2})  # map values using dict
df.applymap(func)               # element-wise on entire DataFrame
```

## Saving / Exporting

```python
df.to_csv('out.csv', index=False)
df.to_excel('out.xlsx', sheet_name='Data', index=False)
df.to_json('out.json', orient='records')
df.to_dict('records')          # list of dicts
df.to_numpy()                  # NumPy array
```

---

## Common Patterns

```python
# Top-N by a column
df.nlargest(5, 'CGPA')
df.nsmallest(3, 'price')

# Reset index after filtering / sorting
df.reset_index(drop=True)

# Check column value counts as percentages
df['branch'].value_counts(normalize=True) * 100

# Cross-tabulation (like a frequency pivot)
pd.crosstab(df['gender'], df['result'])

# Sample rows
df.sample(n=10, random_state=42)
df.sample(frac=0.2, random_state=42)  # 20% of rows
```

---

## Things That Caught Me Out

| Gotcha | Fix |
|--------|-----|
| `df['col']` modifies original but warns | Use `.copy()` when creating subsets |
| `loc[1:3]` **includes** row 3 | Unlike Python slices — label slices are inclusive |
| `iloc[1:3]` **excludes** row 3 | Position slices are exclusive, like Python |
| Chain assignment warning | Avoid `df[df['x']>1]['y'] = 5` — use `loc` instead |
| `inplace=True` is risky | Prefer `df = df.method()` for clarity |
| `groupby` result is not a DataFrame | Call `.reset_index()` to get a clean DataFrame |

---

*Last updated: Phase 1, Learners' Space 2026*
