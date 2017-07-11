# Python Pandas Workshop

## Python introduction

* ask about lists, tuples, dicts

### Lists

```python
>>> numbers = [1, 2, 3]
>>> numbers[0]
1
```

```python
>>> numbers.append(4)
>>> print(numbers)
[1, 2, 3, 4]
>>>
```
### Tuples

```python
# tuples use parentheses
a_tuple= (1, 2, 3)
another_tuple = ('blue', 'green', 'red')
# Note: lists use square brackets
a_list = [1, 2, 3]
```

### Dictionaries

```python
>>> translation = {'one': 1, 'two': 2}
>>> translation['one']
1
```

```python
>>> for key, value in rev.items():
...     print(key, '->', value)
...
1 -> one
2 -> two
3 -> three
```

```python
>>> for key in rev.keys():
...     print(key, '->', rev[key])
...
1 -> one
2 -> two
3 -> three
>>>
```

### Functions


```python
def add_function(a, b):
    result = a + b
    return result

z = add_function(20, 22)
print(z)
42
```

`lambda` lets you define functions in one line.

```python
lambda x: x**2
```

```python
nums = [1 , -7, 3, 12, -3]
positives = filter(lambda num: num > 0, nums)
print(list(positives))
```

See[Python Filter Docs](https://docs.python.org/3/library/functions.html#filter):

Note that `filter(function, iterable)` is equivalent to the generator
expression `(item for item in iterable if function(item))`.

See [Python Functions Aren't What You Think](https://powerfulpython.com/blog/python-functions-arent-what-you-think/)

## Starting With Data

Import the pandas library.

```python
import pandas as pd
```

Read CSV file - data on animals from the Chihuahuan Desert ecosystem.

```python
# note that pd.read_csv is used because we imported pandas as pd
pd.read_csv("surveys.csv")
```

```python
surveys_df = pd.read_csv("surveys.csv")
```

What kind of object is `surveys_df`

```python
type(surveys_df)
# this does the same thing as the above!
surveys_df.__class__
```

What types of data make up the DF?

```python
surveys_df.dtypes
```

Attributes vs. methods in python

* `surveys_df.columns`
* `surveys_df.shape`
* `surveys_df.head()`
* `surveys_df.tail()`

```python
# Look at the column names
surveys_df.columns.values
```

List all the species in the data frame using `pd.unique`

```python
pd.unique(surveys_df['species_id'])
```

Challenge - list of unique plot IDs in the survey data - save as `plot_names`.
Difference between `len(plot_names)` and `plot_names.nunique()`?

### Groups

```python
surveys_df['weight'].describe()
```

```python
surveys_df['weight'].min()
surveys_df['weight'].max()
surveys_df['weight'].mean()
surveys_df['weight'].std()
surveys_df['weight'].count()
```

Group by the sex of the animal.

```python
# Group data by sex
sorted_data = surveys_df.groupby('sex')
```

```python
# summary statistics for all numeric columns by sex
sorted_data.describe()
# provide the mean for each numeric column by sex
sorted_data.mean()
```

Challenge - multiple group.

`sorted_data2 = surveys_df.groupby(['plot_id','sex'])`
`sorted_data2.mean()`

**Not a bad spot to introduce multiple indices, stack and unstack**

First we group data by plot and by sex, and then calculate a total for each plot.

```python
by_plot_sex = surveys_df.groupby(['plot_id','sex'])
plot_sex_count = by_plot_sex['weight'].sum()
```

Below we'll use `.unstack()` on our grouped data to figure out the total weight that each sex contributed to each plot.

```python
by_plot_sex = surveys_df.groupby(['plot_id','sex'])
plot_sex_count = by_plot_sex['weight'].sum()
plot_sex_count.unstack()
```

Summary counts with `groupby` and `count()`

```python
# count the number of samples by species
species_counts = surveys_df.groupby('species_id')['record_id'].count()
print(species_counts)
```

Or, we can also count just the rows that have the species "DO":

```python
surveys_df.groupby('species_id')['record_id'].count()['DO']
```

```python
# multiply all weight values by 2
surveys_df['weight']*2
```

## Indexing, Slicing and Subsetting DataFrames

The square brackets `[]` are called the _indexing operator_ in Python.

### Selecting Column Headings - select with Labels

```python
# Method 1: select a 'subset' of the data using the column name
surveys_df['species_id']

# Method 2: use the column name as an 'attribute'; gives the same output
surveys_df.species_id
```

Select multiple columns with a list of columns.

```python
# select the species and plot columns from the DataFrame
surveys_df[['species_id', 'plot_id']]

# what happens when you flip the order?
surveys_df[['plot_id', 'species_id']]

#what happens if you ask for a column that doesn't exist?
surveys_df['speciess']
```

### Slicing

* Zero based indexing

```python
# Create a list of numbers:
a = [1, 2, 3, 4, 5]

# what does this return?
a[0]

a[5]

a[len(a)]
```

Slicing rows - give `[]` a slice or integer -- and it applies it to the rows!


```python
# select rows 0, 1, 2 (row 3 is not selected)
surveys_df[0:3]
```

```python
# select the first 5 rows (rows 0, 1, 2, 3, 4)
surveys_df[:5]

# select the last element in the list
# (the slice starts at the last element,
# and ends at the end of the list)
surveys_df[-1:]
```

Copying vs. Referencing Objects!

```python
# using the 'copy() method'
true_copy_surveys_df = surveys_df.copy()

# using '=' operator
ref_surveys_df = surveys_df
```

Reassign part of the data frame.

```python
# Assign the value `0` to the first three rows of data in the DataFrame
ref_surveys_df[0:3] = 0
```

Let's try the following code:

```python
# ref_surveys_df was created using the '=' operator
ref_surveys_df.head()

# surveys_df is the original dataframe
surveys_df.head()
```

Okay, that's enough of that. Let's create a brand new clean dataframe from
the original data CSV file.

```python
surveys_df = pd.read_csv("surveys.csv")
```

Row and column slicing - `loc` and `iloc`

```python
# iloc[row slicing, column slicing]
surveys_df.iloc[0:3, 1:4]
```

```python
# select all columns for rows of index values 0 and 10
surveys_df.loc[[0, 10], :]

# what does this do?
surveys_df.loc[0, ['species_id', 'plot_id', 'weight']]

# What happens when you type the code below?
surveys_df.loc[[0, 10, 35549], :]
```

**NOTE**: Labels must be found in the DataFrame or you will get a `KeyError`.

```python
surveys_df.iloc[2, 6]
```
- `surveys_df[0:1]`
- `surveys_df[:4]`
- `surveys_df[:-1]`
- `dat.iloc[0:4, 1:4]`
- `dat.loc[0:4, 1:4]`

```python
surveys_df['month':'year']
```


* slice by label
* `IndexSlice`


Subset data by criteria:

```python
surveys_df[surveys_df.year == 2002]
```

```python
surveys_df[surveys_df.year != 2002]
```

We can define sets of criteria too:

```python
surveys_df[(surveys_df.year >= 1980) & (surveys_df.year <= 1985)]
```

```python
surveys_df[surveys_df['species_id'].isin([listGoesHere])]
```

The `~` symbol in Python can be used to return the OPPOSITE of the
selection that you specify in Python. It is equivalent to **is not in**.
Write a query that selects all rows with sex NOT equal to 'M' or 'F' in
the "surveys" data.

```python
surveys_dfl[~surveys_df['sex'].isin(['M', 'F'])]
```

Boolean Masks

```python
pd.isnull(surveys_df)
```

```python
# To select just the rows with NaN values, we can use the 'any()' method
surveys_df[pd.isnull(surveys_df).any(axis=1)]
```

```python
# what does this do?
empty_weights = surveys_df[pd.isnull(surveys_df['weight'])]['weight']
print(empty_weights)
```

## Data Types and Formats

```python
type(surveys_df)
```

```python
surveys_df['sex'].dtype
```

```python
surveys_df.dtypes
```

```python
# convert the record_id field from an integer to a float
surveys_df['record_id'] = surveys_df['record_id'].astype('float64')
surveys_df['record_id'].dtype
```

```python
df1 = surveys_df.copy()
# fill all NaN values with 0
df1['weight'] = df1['weight'].fillna(0)
```

* Categorical Data Types
* Date Time

```python
# relies on data being in year, month, date columns
survey_df['date'] = pd.to_datetime(survey_df)
```

## Combining DataFrames
```python
import pandas as pd
surveys_df = pd.read_csv("surveys.csv",
                         keep_default_na=False, na_values=[""])
surveys_df
```

```python
species_df = pd.read_csv("species.csv",
                         keep_default_na=False, na_values=[""])
species_df
```

```python
# read in first 10 lines of surveys table
survey_sub = surveys_df.head(10)
# grab the last 10 rows 
survey_sub_last10 = surveys_df.tail(10)
#reset the index values to the second dataframe appends properly
survey_sub_last10=survey_sub_last10.reset_index(drop=True)
# drop=True option avoids adding new index column with old index values
```

```python
# stack the DataFrames on top of each other
vertical_stack = pd.concat([survey_sub, survey_sub_last10], axis=0)
```

Have a look at the `vertical_stack` dataframe? Notice anything unusual?
The row indexes for the two data frames `survey_sub` and `survey_sub_last10`
have been repeated. We can reindex the new dataframe using the `reset_index()` method.

```python
# place the DataFrames side by side
horizontal_stack = pd.concat([survey_sub, survey_sub_last10], axis=1)
```

```python
# Write DataFrame to CSV
vertical_stack.to_csv('out.csv', index=False)
```

```python
# for kicks read our output back into python and make sure all looks good
new_output = pd.read_csv('out.csv', keep_default_na=False, na_values=[""])
```

```python
# read in first 10 lines of surveys table
survey_sub = surveys_df.head(10)

# import a small subset of the species data designed for this part of the lesson.
# It is stored in the data folder.
species_sub = pd.read_csv('data/speciesSubset.csv', keep_default_na=False, na_values=[""])
```

Check common columns!

`species_sub.columns`

`survey_sub.columns`

```python
merged_inner = pd.merge(left=survey_sub,right=species_sub, left_on='species_id', right_on='species_id')
# in this case `species_id` is the only column name in  both dataframes, so if we skippd `left_on`
# and `right_on` arguments we would still get the same result

# what's the size of the output data?
merged_inner.shape
merged_inner
```

```python
merged_left = pd.merge(left=survey_sub,right=species_sub, how='left', left_on='species_id', right_on='species_id')

merged_left
```
