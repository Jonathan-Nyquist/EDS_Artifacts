title: Sample review — arrays, tables, functions, conditionals, loops

<!--
FORMAT
  title: ...            optional, before the first category
  # Category name       starts a column; optional color: # Tables {#CAFF70}
  ## 300                a clue worth 300 (the "$" is optional)
  Q: ...                the clue; can span lines and include ```python blocks
  A: ...                the answer
  E: ...                optional explanation shown with the answer
  `code`  **bold**      inline formatting
  HTML comments like this one are ignored.
Columns can have different numbers of clues; rows are the union of all values.
-->

# Arrays {#FFE14D}

## 100
Q: What does this return?
```python
make_array(3, 6, 9).item(1)
```
A: `6`
E: Arrays are zero-indexed, so item 1 is the second element.

## 200
Q: What does this return?
```python
make_array(10, 20, 30) / 10
```
A: `array([1., 2., 3.])`
E: Arithmetic on an array applies to every element. Division always produces floats.

## 300
Q: What does `np.arange(2, 10, 3)` return?
A: `array([2, 5, 8])`
E: Start at 2, step by 3, and stop *before* 10.

## 400
Q: What is in this array?
```python
np.arange(3)**3
```
A: `array([0, 1, 8])`
E: arange generates array(0, 1, 2) then each value is cubed.

## 500
Q: Daily discharge at a gauge, in cfs. What does this return?
```python
flow = make_array(3.2, 5.0, 4.1, 9.8)
np.count_nonzero(np.diff(flow) > 0)
```
A: `2`
E: `np.diff` gives day-to-day changes: 1.8, −0.9, 5.7. Two of them are increases — two rising days.

# Tables {#CAFF70}

## 100
Q: How do you get the number of rows in a Table `t`?
A: `t.num_rows`
E: It's an attribute, not a method — no parentheses.

## 200
Q: Does `t.column('pH')` return a Table or an array?
A: An array
E: `t.select('pH')` is the version that returns a one-column Table.

## 300
Q: Table `samples`:
```text
Site        | Season | Chloride (mg/L)
Wissahickon | Winter | 412
Pennypack   | Winter | 958
Tacony      | Summer | 187
Cobbs Creek | Winter | 640
```
What does this return?
```python
samples.sort('Chloride (mg/L)', descending=True).column('Site').item(0)
```
A: `'Pennypack'`
E: Sort high-to-low, take the Site column as an array, take its first entry: the site with the most chloride.

## 400
Q: Table `stations`:
```text
Station | Watershed
WS-1    | Wissahickon
WS-2    | Wissahickon
PP-1    | Pennypack
WS-3    | Wissahickon
```
What does `stations.group('Watershed')` return?
A: A two-column table:
```text
Watershed   | count
Pennypack   | 1
Wissahickon | 3
```
E: One row per unique value, sorted, with `count` giving how many rows had it. The Station column disappears.

## 500
Q: Same `samples` table:
```text
Site        | Season | Chloride (mg/L)
Wissahickon | Winter | 412
Pennypack   | Winter | 958
Tacony      | Summer | 187
Cobbs Creek | Winter | 640
```
Write one expression that gives the mean chloride for each season.
A: `samples.select('Season', 'Chloride (mg/L)').group('Season', np.mean)`
E: Summer 187, Winter 670. `group` with a function applies it to every other column; selecting first keeps `np.mean` away from the text column Site.

# Functions {#FF90E8}

## 100
Q: What keyword begins a function definition in Python?
A: `def`
E: `def name(arguments):` followed by an indented body.

## 200
Q: What is stored in `y`?
```python
def double(x):
    print(x * 2)

y = double(5)
```
A: `None`
E: `print` displays 10 but doesn't hand anything back. A function without `return` returns `None`.

## 300
Q: What does this return?
```python
def celsius_to_kelvin(c):
    return c + 273.15

celsius_to_kelvin(25) - celsius_to_kelvin(20)
```
A: `5.0`
E: The 273.15 offsets cancel. A temperature *difference* is the same in °C and K.

## 400
Q: Table `streams`:
```text
Site        | Discharge (cfs)
Wissahickon | 35.3
Pennypack   | 70.6
```
Write the call that applies the function `cfs_to_cms` to every value in the Discharge column.
A: `streams.apply(cfs_to_cms, 'Discharge (cfs)')`
E: Pass the function itself — no parentheses after its name. The result is an array, about 1.0 and 2.0 m³/s.

## 500
Q: What does this return?
```python
def scale(arr, factor=2):
    arr = arr * factor
    return arr.mean()

temps = make_array(10, 20, 30)
scale(temps, 3) + temps.item(0)
```
A: `60.0`
E: `scale` returns the mean of 30, 60, 90, which is 60.0.

# Conditionals {#90A8FF}

## 100
Q: What does `7 > 3` evaluate to, and what type is it?
A: `True`, a `bool`

## 200
Q: What is `label`?
```python
ph = 5.4
if ph < 7:
    label = 'acidic'
elif ph < 6:
    label = 'strongly acidic'
else:
    label = 'neutral or basic'
```
A: `'acidic'`
E: Only the first true branch runs. The `elif` is never checked, so this ordering can never produce 'strongly acidic'.

## 300
Q: What does this return?
```python
sum(make_array(4.1, 7.8, 6.5, 8.2) > 7)
```
A: `2`
E: The comparison gives an array of booleans. `True` counts as 1 when summed.

## 400
Q: Dissolved oxygen in mg/L. What does this return?
```python
def classify(do):
    if do >= 5:
        return 'healthy'
    if do >= 2:
        return 'stressed'
    return 'hypoxic'

classify(5) + ' ' + classify(1.9)
```
A: `'healthy hypoxic'`
E: `return` exits immediately, so the second `if` behaves like an `elif`. 5 meets `>= 5`; 1.9 falls through both.

## 500
Q: Table `chloride`:
```text
Site        | Chloride (mg/L)
Tacony      | 187
Darby Creek | 230
Wissahickon | 412
Cobbs Creek | 860
Pennypack   | 958
```
Which sites does this keep?
```python
chloride.where('Chloride (mg/L)', are.between(230, 860))
```
A: Darby Creek and Wissahickon
E: `are.between` includes the lower bound and excludes the upper, so 230 stays and 860 goes. (230 and 860 mg/L are the EPA chronic and acute chloride criteria.)

# Loops {#FF7051}

## 100
Q: How many times does the loop body run?
```python
for i in np.arange(5):
    ...
```
A: 5
E: `np.arange(5)` is 0, 1, 2, 3, 4.

## 200
Q: What is `total`?
```python
total = 0
for depth in make_array(2, 4, 6):
    total = total + depth
```
A: `12`

## 300
Q: What is `counts`?
```python
counts = make_array()
for trial in np.arange(3):
    counts = np.append(counts, trial * 10)
```
A: `array([ 0., 10., 20.])`
E: `np.append` returns a new array, so you must reassign it. Starting from an empty array gives floats.

## 400
Q: A germination simulation. What is `len(germinated)`, and what's the bug?
```python
for x in np.arange(5):
    total = make_array()
    total = np.append(total, x)
total
```
A: `array([4.])` — the array is reset on every pass
E: Create the collection array once, *before* the loop.

## 500
Q: What is `hits`?
```python
hits = 0
for i in np.arange(1, 11):
    if i % 3 == 0:
        hits = hits + i
    elif i % 2 == 0:
        hits = hits - 1
```
A: `14`
E: 3, 6, 9 add 18. The evens not divisible by 3 (2, 4, 8, 10) subtract 4.
