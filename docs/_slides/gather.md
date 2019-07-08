---
---

## From wide to long

The pandas package’s melt function reshapes “wide” data frames into “long” ones.

https://datascience.stackexchange.com/questions/10478/is-there-any-data-tidying-tool-for-python-pandas-similar-to-r-tidyr-tool



~~~python
> import pandas as pd
+ import numpy as np
+ trial_df = pd.DataFrame({"block": [1,2,3],
+               "drug": [0.22,0.12,0.42],
+               "control": [0.58,0.98,0.19],
+               "placebo": [0.31,0.47,0.40]})
+ 
+ trial_df
~~~
{:title="Console" .input}


~~~
   block  control  drug  placebo
0      1     0.58  0.22     0.31
1      2     0.98  0.12     0.47
2      3     0.19  0.42     0.40
~~~
{:.output}


~~~python
> tidy_trial_df = pd.melt(trial_df,
+                   id_vars=['block'],
+                   var_name='treatment',
+                   value_name='response')
+ tidy_trial_df
~~~
{:title="Console" .input}


~~~
   block treatment  response
0      1   control      0.58
1      2   control      0.98
2      3   control      0.19
3      1      drug      0.22
4      2      drug      0.12
5      3      drug      0.42
6      1   placebo      0.31
7      2   placebo      0.47
8      3   placebo      0.40
~~~
{:.output}


All columns, accept for “block”, are stacked in two columns: a “key” and a “value”. The key column gets the name treatment and the value column reveives the name response. For each row in the result, the key is taken from the name of the column and the value from the data in the column.

## From long to wide

Data can also fail to be tidy when a table is too long. The Entity-Attribute-Value (EAV) structure common in large databases distributes multible attributes of a single entity/observation into separate rows.

Remember that the exact state of “tidy” may depend on the analysis: the key is knowing what counts as a complete observation. For example, the community ecology package vegan requires a matrix of species counts, where rows correspond to species and columns to sites. This may seem like too “wide” a format, but in the packages several multi-variate analyses, the abundance of a species across multiple sites is considered a complete observation.

Consider survey data on participant’s age and income stored in a EAV structure.



~~~python
> 
+ df2 = tidy_trial_df.pivot(index='block',
+                           columns='treatment',
+                           values='response')
+ df2 = df2.reset_index()
+ df2.columns
~~~
{:title="Console" .input}


~~~
Index(['block', 'control', 'drug', 'placebo'], dtype='object', name='treatment')
~~~
{:.output}


~~~python
> df2.reset_index()
~~~
{:title="Console" .input}


~~~
treatment  index  block  control  drug  placebo
0              0      1     0.58  0.22     0.31
1              1      2     0.98  0.12     0.47
2              2      3     0.19  0.42     0.40
~~~
{:.output}


~~~python
> df2
~~~
{:title="Console" .input}


~~~
treatment  block  control  drug  placebo
0              1     0.58  0.22     0.31
1              2     0.98  0.12     0.47
2              3     0.19  0.42     0.40
~~~
{:.output}


Consider survey data on participant's age and income *stored* in a EAV structure.



~~~python
> 
+ #import sys
+ #from io import StringIO
+ #import pandas as pd
+ from pandas.compat import StringIO, BytesIO
+ 
+ text_string = StringIO("""
+ participant,attr,val
+ 1,age,24
+ 2,age,57
+ 3, age,13
+ 1,income,30
+ 2,income,60
+ """)
+ 
+ survey_df = pd.read_csv(text_string, sep=",")
+ survey_df
~~~
{:title="Console" .input}


~~~
   participant    attr  val
0            1     age   24
1            2     age   57
2            3     age   13
3            1  income   30
4            2  income   60
~~~
{:.output}


Transform the data with the `spread` function, which "reverses" a `gather`.



~~~python
> 
+ 
+ tidy_survey = survey_df.pivot(index='participant',
+                           columns='attr',
+                           values='val')
+ tidy_survey = tidy_survey.reset_index()
+ tidy_survey.columns
~~~
{:title="Console" .input}


~~~
Index(['participant', ' age', 'age', 'income'], dtype='object', name='attr')
~~~
{:.output}


~~~python
> tidy_survey.reset_index()
~~~
{:title="Console" .input}


~~~
attr  index  participant   age   age  income
0         0            1   NaN  24.0    30.0
1         1            2   NaN  57.0    60.0
2         2            3  13.0   NaN     NaN
~~~
{:.output}


~~~python
> tidy_survey
~~~
{:title="Console" .input}


~~~
attr  participant   age   age  income
0               1   NaN  24.0    30.0
1               2   NaN  57.0    60.0
2               3  13.0   NaN     NaN
~~~
{:.output}


<!-- === -->

<!-- Question -->
<!-- : Why were `attr` and `val` not quoted in the call to `spread`? -->

<!-- Answer -->
<!-- : {:.fragment} They refer to existing column names. In `gather`, quotes are used -->
<!-- to create new column names. -->

<!-- === -->

<!-- One difficulty with EAV tables is the nature of missing data; an entire row -->
<!-- rather than a single cell is missing. Think about what "missing data" could mean -->
<!-- here---perhaps you can supply a value instead of the `NA` in the previous -->
<!-- result. -->

<!-- ```{r, handout = 0} -->
<!-- tidy_survey <- spread(survey, -->
<!--   key = attr, -->
<!--   value = val, -->
<!--   fill = 0) -->
<!-- ``` -->

<!-- === -->

<!-- ```{r} -->
<!-- tidy_survey -->
<!-- ``` -->

