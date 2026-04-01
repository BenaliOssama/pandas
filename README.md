# NumPy Piscine — Project Reference

A day-long project working through NumPy from first principles. Nine exercises covering
array creation, slicing, broadcasting, random generation, NaN handling, and real data analysis.

---

## Environment Setup

### Create and activate the virtual environment
```bash
python3 -m venv ex00
source ex00/bin/activate
```

### Install dependencies
```bash
pip install numpy jupyter
pip freeze > ex00/requirements.txt
```

### Launch Jupyter
```bash
jupyter notebook --port 8891
```

### Deactivate the environment when done
```bash
deactivate
```

---

## Project Structure

```
project/
├── .gitignore
├── README.md
├── ex00/                        ← virtual environment + setup notebook
│   ├── requirements.txt
│   └── Notebook_ex00.ipynb
├── ex01/
│   └── Notebook_ex01.ipynb
├── ex02/
│   └── Notebook_ex02.ipynb
├── ex03/
│   └── Notebook_ex03.ipynb
├── ex04/
│   └── Notebook_ex04.ipynb
├── ex05/
│   └── Notebook_ex05.ipynb
├── ex06/
│   └── Notebook_ex06.ipynb
├── ex07/
│   └── Notebook_ex07.ipynb
├── ex08/
│   ├── Notebook_ex08.ipynb
│   └── data/
│       ├── winequality-red.csv
│       └── winequality.names
└── ex09/
    ├── Notebook_ex09.ipynb
    └── data/
        └── model_forecasts.txt
```

---

## .gitignore

```gitignore
# Virtual environment internals (not the folder itself)
ex00/bin/
ex00/lib/
ex00/include/
ex00/pyvenv.cfg

# Python bytecode cache
__pycache__/
*.pyc
*.pyo

# Jupyter autosave
.ipynb_checkpoints/

# OS noise
.DS_Store
```

---

## Core Concept — Why NumPy Exists

Python lists can hold anything — strings, numbers, objects, mixed types. That flexibility
costs performance. Every element in a Python list is a full Python object with its own
type information and memory overhead.

NumPy solves this by enforcing that every element in an array is the same type. This lets
NumPy store elements as raw bytes in a contiguous block of memory and hand the whole block
to optimized C code. The result is operations that are 10-100x faster than Python loops.

Everything else in NumPy — slicing, broadcasting, random generation — is built on top of
that one idea.

---

## Exercise Summaries

### ex00 — Environment Setup
Set up a virtualenv, install numpy and jupyter, launch a notebook on port 8891.
Create markdown cells (# for h1, ## for h2) and code cells (Shift+Enter to run).

**Key idea:** A virtual environment is just a folder with its own Python and packages.
`source ex00/bin/activate` tells your terminal to use that folder's Python instead of the global one.

---

### ex01 — Your First NumPy Array
Create a NumPy array holding mixed types: int, float, string, dict, list, tuple, set, bool.

```python
import numpy as np

your_numpy_array = np.array([
    42, 3.14, "hello", {"key": 1},
    [1, 2, 3], (4, 5, 6), {7, 8, 9}, True
], dtype=object)

for i in your_numpy_array:
    print(type(i))
```

**Key idea:** When types are incompatible, NumPy falls back to `dtype=object` — storing
pointers to Python objects instead of raw values. You lose all performance benefits.
NumPy infers this automatically even without `dtype=object`.

---

### ex02 — Zeros
Create an array of 300 zeros and reshape it to (3, 100).

```python
zeros = np.zeros(300)
reshaped = zeros.reshape(3, 100)
```

**Key idea:** Reshape doesn't move data in memory. It just changes how NumPy interprets
the same flat block of bytes — "treat this as 3 rows of 100 elements each."

---

### ex03 — Slicing
Create an array 1-100, extract odds, extract evens reversed, zero every 3rd element.

```python
a = np.arange(1, 101)
odds = a[::2]                # every 2nd element from start
evens_reversed = a[100::-2]  # from index 100, step backwards by 2
a[1::3] = 0                  # set every 3rd element starting at index 1 to zero
```

**Key idea:** Slicing syntax is `[start:stop:step]`. Negative step means go backwards.
NumPy is forgiving with out-of-bound slice starts — it clamps to the last valid index.

---

### ex04 — Random Generation
Generate random arrays using normal and uniform distributions.

```python
np.random.seed(888)                              # set seed for reproducibility
normal_array = np.random.randn(100)              # 1D, normal distribution
int_2d = np.random.randint(1, 11, size=(8, 8))  # 2D, integers 1-10 inclusive
int_3d = np.random.randint(1, 18, size=(4,2,5)) # 3D, integers 1-17 inclusive
```

**Key ideas:**
- Seed: random generators are deterministic algorithms. Same seed = same sequence.
  Critical for reproducibility.
- Normal distribution: values cluster around a mean (most natural phenomena).
- Uniform distribution: every value has equal probability.
- `randint(low, high)` — high is exclusive. To include 10, write `randint(1, 11)`.

---

### ex05 — Concatenate and Reshape
Generate two arrays, join them, reshape into a 10x10 grid.

```python
a = np.arange(1, 51)
b = np.arange(51, 101)
c = np.concatenate([a, b])
d = c.reshape(10, 10)
```

**Key idea:** `reshape` requires the total number of elements to match.
10 × 10 = 100, which is exactly how many elements we have.

---

### ex06 — Broadcasting and Slicing
Create a 9x9 ones array, carve a nested square pattern, then build a multiplication table.

```python
# Nested square pattern
a = np.ones((9, 9), dtype=np.int8)
a[1:8, 1:8] = 0
a[2:7, 2:7] = 1
a[3:6, 3:6] = 0
a[4, 4]     = 1

# Broadcasting — multiplication table
array_1 = np.array([1,2,3,4,5], dtype=np.int8)
array_2 = np.array([1,2,3], dtype=np.int8)
result = array_1.reshape(5, 1) * array_2
```

**Key idea:** Broadcasting lets NumPy do math between arrays of different shapes.
`reshape(5, 1)` turns a row vector into a column vector. NumPy then stretches both
arrays to matching shapes and multiplies element-wise — no loop needed.

---

### ex07 — NaN Handling
Handle missing exam grades using `np.where` — no loops or if/else allowed.

```python
generator = np.random.default_rng(123)
grades = np.round(generator.uniform(low=0.0, high=10.0, size=(10, 2)))
grades[[1,2,5,7], [0,0,0,0]] = np.nan

third_col = np.where(np.isnan(grades[:, 0]), grades[:, 1], grades[:, 0])
grades = np.column_stack([grades, third_col])
```

**Key ideas:**
- NaN is infectious: `5 + NaN = NaN`. Always handle it explicitly.
- `np.where(condition, value_if_true, value_if_false)` — vectorized ternary operator.
- `np.isnan()` — produces a True/False array, True wherever a value is NaN.

---

### ex08 — Wine Dataset Analysis
Load a real CSV and answer 7 questions using NumPy statistical functions.

**Column index reference:**
```
0  fixed acidity      4  chlorides          8  pH
1  volatile acidity   5  free sulfur dioxide 9  sulphates
2  citric acid        6  total sulfur dioxide 10 alcohol
3  residual sugar     7  density            11 quality
```

```python
# Load
data = np.genfromtxt('data/winequality-red.csv',
                      delimiter=';', skip_header=True, dtype=np.float32)

# Specific rows (zero-indexed)
rows = data[[1, 6, 11], :]
clean_rows = rows[~np.isnan(rows).any(axis=1)]

# Boolean question
np.any(data[:, 10] > 20)

# Average (NaN-safe)
np.nanmean(data[:, 10])

# Percentile stats
np.nanpercentile(data[:, 8], 25)

# Mask + mean
threshold = np.nanpercentile(data[:, 9], 20)
mask = data[:, 9] < threshold
np.nanmean(data[mask, 11])

# Group means
best_mask = data[:, 11] == np.nanmax(data[:, 11])
np.nanmean(data[best_mask], axis=0)  # axis=0 = one mean per column
```

**Key ideas:**
- `axis=0` collapses downward (across rows) → one result per column.
- `axis=1` collapses rightward (across columns) → one result per row.
- Always use `nan`-prefixed functions (`nanmean`, `nanmin` etc.) on real data.
- Boolean masks: create a True/False array, use it to filter rows.

---

### ex09 — Football Tournament (Permutations)
Find the most balanced pairing of 10 teams by evaluating all 3.6 million possible orderings.

```python
import numpy as np
import itertools

data = np.loadtxt('data/model_forecasts.txt', delimiter=',')
teams = np.arange(10)

# Generate all orderings
permutations = np.array(list(itertools.permutations(teams)))  # (3628800, 10)

# Split into two teams per match
team1 = permutations[:, 0::2]  # (3628800, 5)
team2 = permutations[:, 1::2]  # (3628800, 5)

# Look up score differences using fancy indexing
differences = data[team1, team2]  # (3628800, 5)

# Score each pairing — square to punish unbalanced games, sum across 5 matches
scores = np.sum(differences ** 2, axis=1)  # (3628800,)

# Find the best pairing
best_index = np.argmin(scores)
best_pairing = permutations[best_index]

# Format as 2x5 output
result = best_pairing.reshape(2, 5, order='F')
print(result)
```

**Key ideas:**
- A permutation is just a different ordering of the same elements.
  Reading consecutive pairs from an ordering gives a valid match pairing.
- Fancy indexing: passing two arrays as indices does element-wise lookup across both simultaneously.
- `np.argmin` returns the *index* of the minimum value, not the value itself.
- `reshape(order='F')` fills the array column by column (Fortran style)
  instead of row by row (C style, the default).
- Squaring differences removes negatives AND punishes large imbalances heavily,
  pushing toward pairings where all matches are balanced — not just the average.

---

## Quick NumPy Reference

```python
# Array creation
np.array([1, 2, 3])          # from list
np.zeros(300)                 # all zeros
np.ones((9, 9), dtype=np.int8)  # all ones, specific type
np.arange(1, 101)             # integers 1 to 100

# Shape and memory
a.shape    # dimensions
a.dtype    # data type
a.nbytes   # memory usage in bytes

# Slicing
a[::2]        # every 2nd element
a[1::3]       # every 3rd element starting at index 1
a[100::-2]    # from index 100, backwards by 2
a[1:8, 1:8]  # 2D slice: rows 1-7, columns 1-7

# Math
a ** 2                        # square every element
np.sum(a, axis=0)             # sum down rows → one value per column
np.sum(a, axis=1)             # sum across columns → one value per row

# Statistics (NaN-safe versions)
np.nanmean(a)
np.nanmin(a) / np.nanmax(a)
np.nanpercentile(a, 25)

# Boolean operations
np.any(a > 20)                # is any value > 20?
np.isnan(a)                   # True wherever NaN
a[~np.isnan(a)]               # filter out NaN values

# Combining arrays
np.concatenate([a, b])        # join end to end
np.column_stack([a, b, c])    # join as columns

# Where
np.where(condition, val_if_true, val_if_false)

# Random
np.random.seed(888)
np.random.randn(100)          # normal distribution
np.random.randint(1, 11, size=(8, 8))  # integers, high is exclusive

# Finding things
np.argmin(a)   # index of minimum value
np.argmax(a)   # index of maximum value
```
