# NumPy — Quick Reference Notes
**Analytics Club | Learners' Space 2026**

These are my personal notes while going through the NumPy module. Not a tutorial — just things I want to be able to look up fast.

---

## Creating Arrays

```python
import numpy as np

np.array([1, 2, 3])              # from list → 1D
np.array([[1,2],[3,4]])          # from nested list → 2D
np.zeros((3, 4))                 # 3×4 of zeros
np.ones((2, 3))                  # 2×3 of ones
np.eye(4)                        # 4×4 identity matrix
np.full((2, 3), 7)               # 2×3 filled with 7
np.arange(0, 10, 2)              # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)             # 5 evenly spaced between 0 and 1
np.random.randint(1, 101, (3,3)) # 3×3 random ints 1–100
np.random.randn(4, 4)            # 4×4 standard normal
```

## Array Attributes

```python
arr.shape     # (rows, cols) — always a tuple
arr.ndim      # number of dimensions (1 for 1D, 2 for 2D, ...)
arr.size      # total number of elements
arr.dtype     # data type (int64, float64, bool, ...)
arr.itemsize  # bytes per element
arr.nbytes    # total bytes (= size × itemsize)
```

## Indexing and Slicing

```python
arr[2]          # 1D: 3rd element
arr[1, 3]       # 2D: row 1, col 3
arr[0, :]       # entire first row
arr[:, 2]       # entire third column
arr[1:3, 0:2]   # rows 1-2, cols 0-1
arr[::-1]       # 1D reversed
arr[::2, ::2]   # every other row and column
arr[-1, -1]     # last row, last column
```

### Boolean / Fancy Indexing

```python
arr[arr > 50]                     # elements > 50
arr[(arr > 20) & (arr < 80)]      # chained conditions (use & not 'and')
np.where(arr > 50, 'high', 'low') # vectorised if-else
arr[[0, 2, 4]]                    # select rows 0, 2, 4 by index list
```

## Reshaping

```python
arr.reshape(3, 4)    # new shape — total elements must match
arr.reshape(-1)      # flatten to 1D
arr.reshape(2, -1)   # 2 rows, NumPy figures out cols
arr.flatten()        # always returns a copy
arr.ravel()          # returns a view when possible (faster)
arr.T                # transpose
```

## Arithmetic — All Vectorised

```python
a + b    # element-wise addition
a - b    # subtraction
a * b    # multiplication (NOT matrix multiply!)
a / b    # division
a ** 2   # element-wise power
a @ b    # matrix multiplication (Python 3.5+)
np.dot(a, b)  # same as a @ b
```

### Broadcasting Rules

1. Arrays are aligned from the **right**
2. Dimensions must be equal, or one of them must be **1**
3. Dimension of size 1 is stretched to match the other

```python
(4, 3) + (3,)    → OK   → (4, 3)
(4, 1) + (1, 3)  → OK   → (4, 3)
(4, 3) + (4,)    → ERROR
```

## Aggregation

```python
arr.sum()           # total sum
arr.mean()          # mean
arr.std()           # standard deviation
arr.var()           # variance
arr.min()           # minimum
arr.max()           # maximum
arr.argmin()        # index of minimum
arr.argmax()        # index of maximum
arr.cumsum()        # cumulative sum

# All support axis= argument
arr.sum(axis=0)     # sum down columns → (1 × cols)
arr.sum(axis=1)     # sum across rows  → (rows × 1)
```

## Universal Functions (ufuncs)

```python
np.sqrt(arr)      np.abs(arr)
np.exp(arr)       np.log(arr)       np.log10(arr)
np.sin(arr)       np.cos(arr)       np.tan(arr)
np.floor(arr)     np.ceil(arr)      np.round(arr, 2)
np.clip(arr, 0, 100)   # clamp values between 0 and 100
```

## Linear Algebra

```python
np.linalg.det(A)         # determinant
np.linalg.inv(A)         # inverse
np.linalg.eig(A)         # eigenvalues and eigenvectors
np.linalg.solve(A, b)    # solve Ax = b
np.linalg.norm(v)        # vector norm (default: L2)
np.linalg.matrix_rank(A) # rank of matrix
```

## Stacking and Splitting

```python
np.vstack([a, b])     # stack vertically (add rows)
np.hstack([a, b])     # stack horizontally (add cols)
np.concatenate([a, b], axis=0)   # general concatenation
np.split(arr, 3)      # split into 3 equal parts
np.vsplit(arr, 2)     # split rows
np.hsplit(arr, 2)     # split columns
```

## Random (Modern API)

```python
rng = np.random.default_rng(seed=42)   # reproducible generator
rng.random((3, 3))                     # uniform [0, 1)
rng.integers(0, 100, size=(3, 3))      # random ints
rng.normal(mean, std, size)            # Gaussian
rng.choice(arr, size=5, replace=False) # sample without replacement
rng.shuffle(arr)                       # in-place shuffle
```

---

## Things That Caught Me Out

| Gotcha | Correct behaviour |
|--------|-------------------|
| `arr[1:3]` in 2D | Selects **rows** 1 and 2, all columns |
| `a * b` for matrices | **Element-wise** — use `a @ b` for matrix multiply |
| Slicing returns a **view** | Modifying the slice modifies the original — use `.copy()` |
| `axis=0` | Operates **down** the rows (column-wise result) |
| `axis=1` | Operates **across** columns (row-wise result) |
| Boolean: `&` not `and` | `arr[(arr>1) & (arr<5)]` — parentheses required |

---

*Last updated: Phase 1, Learners' Space 2026*
