# Avoiding ‘side effects’:

## Writing functions that are easier to test

2020-09-09

Will Furnass
Research Software Engineer
RSE team

---
## A linear workflow

```python
data = read_data('path/to/input/data.csv')
a, b, c = 1.2, 3.66, 1.0
# ...
# Some statements
# that
# do
# computational
# work
# ...
plot_graphs(x_out, y_out)
write_data('path/to/output/data.csv', [x_out, y_out])
```

--
How to check:

  * it runs without crashing?
  * it generates the right (or just sensible) outputs given good inputs?
  * it does a sensible thing if given bad inputs?

---
## Testing the full workflow

1. Ensure `path/to/input/data.csv` exists
1. Run script
1. Interactively inspect plots
1. Inspect `path/to/output/data.csv` 

Cumbersome, particularly if primarily wanting to test computational part...

---
## Create function(s)

```python
def computational_work(data, a, b, c):
    """Process a dataframe using three params and return two arrays."""
    # ...
    return x_out, y_out

data = read_data('path/to/input/data.csv')
x_out, y_out = computational_work(data, 1.2, 3.66, 1.0)
plot_graphs(x_out, y_out)
write_data('path/to/output/data.csv', [x_out, y_out])
```

--
Allows for manual or automatic periodic checks of the complicated bit:
```
assert computational_work(testdata, 1.0, 1.2, 1.9)[1].min() < 1e8
assert computational_work(testdata, 10.0, 1.2, 1.9)[1].max() > 1.0
```

---
## What makes functions harder to test?

Functions that internally:

  * Read/write global variables
  * Read/write files
  * Communicate with remote servers (e.g. databases, web services)
  * (Use random numbers)

--

i.e. interactions with things outside the function that have a *state*.

These functions are said to have *side effects*.

---
## Functions that are easier to test

```python
def is_leap_year(year):
    """Determine whether a year is a leap year."""
    result = year % 4 == 0 and (year % 100 != 0 or year % 400 == 0)
    return result
```
* A **pure** function with no **side effects**:
    * The return value depends only on the parameter values
    * NOT on your filesystem state, database state, the phase of the moon etc.
* Much more like a function in mathematics.

---
## Suggestions for those coding in Python (and other languages)

Try to write the more computational parts of your code using pure functions, 
limiting the use of side effects to specific places.

---
## Caveats/limitations
  * Returning large values from functions (rather than impurely modifying inputs) may be very inefficient)
  * Functions that generate exceptions aren't strictly pure
  * Testing/reasoning about functions that use random numbers is a topic for another session!

---
## These slides

* [https://willfurnass.github.io/avoiding-side-effects](https://willfurnass.github.io/avoiding-side-effects)

## Further reading

* Chiusano, P. and Bjarnason, R. (2014). *Functional Programming in Scala*, Manning Publications, ISBN:978-1-61729-065-7.
    * First chapter(s) on *referential transparency*
