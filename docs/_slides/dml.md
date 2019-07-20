---
---

## Key Functions

| Function    | Returns                                            |
|-------------+----------------------------------------------------|
| `query`    | keep rows that satisfy conditions                  |
| `assign`    | apply a transformation to existing [split] columns |
| `['col1', 'col2']`| select and keep columns with matching names    |
| `merge`| merge columns from separate tables into one table  |
| `groupby`  | split data into groups by an existing factor       |
| `agg` | summarize across rows to use after groupby [and combine split groups]   |

The table above summarizes the most commonly used functions in
[pandas](){:.pylib}, which we will demonstrate in turn on data from the U.S.
Census Bureau.
{:.notes}

===

### Filter and pattern matching

The `cbp` table includes character `NAICS` column. Of the 2 million
observations, lets see how many observations are left when we keep only the
2-digit NAICS codes, representing high-level sectors of the economy.



~~~python

#import pandas as pd
cbp2 = cbp[cbp['NAICS'].str.contains("----")]
cbp2 = cbp2[~cbp2.NAICS.str.contains("-----")]
cbp2.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
   FIPSTATE FIPSCTY   NAICS EMPFLAG  ... N1000_3  N1000_4 CENSTATE  CENCTY
1        01     001  11----          ...       0        0       63       1
10       01     001  21----          ...       0        0       63       1
17       01     001  22----          ...       0        0       63       1
27       01     001  23----          ...       0        0       63       1
93       01     001  31----          ...       0        0       63       1

[5 rows x 26 columns]
~~~
{:.output}


Note that a logical we used the function `contains` from pandas to filter the
dataset in two steps. The function contains allows for pattern matching of any
character within strings. The `~` is used to remove the rows that contains
specific patterns.
{:.notes}

===

Filtering string often uses pattern matching by [regular expressions]
which may be a bit more manageable, and streamlines the operations.



~~~python
cbp3 = cbp[cbp['NAICS'].str.contains('[0-9]{2}----')]
cbp3.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
   FIPSTATE FIPSCTY   NAICS EMPFLAG  ... N1000_3  N1000_4 CENSTATE  CENCTY
1        01     001  11----          ...       0        0       63       1
10       01     001  21----          ...       0        0       63       1
17       01     001  22----          ...       0        0       63       1
27       01     001  23----          ...       0        0       63       1
93       01     001  31----          ...       0        0       63       1

[5 rows x 26 columns]
~~~
{:.output}


[regular expressions]: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.contains.html#pandas.Series.str.contains

===

### Altering, updating and transforming columns

The `assign` function is the [pandas](){:.pylib} answer to updating or altering
your columns. It performs arbitrary operations on existing columns and appends
the result as a new column of the same length.

===

Here are two ways to create a new column using `assign` and the `[ ]` operators.



~~~python
cbp3["FIPS"] = cbp3["FIPSTATE"]+cbp3["FIPSCTY"]
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
/usr/bin/python3:1: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
~~~
{:.output}


~~~python
cbp3.assign(FIPS2=lambda x: x['FIPSTATE']+x['FIPSCTY'])
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
        FIPSTATE FIPSCTY   NAICS EMPFLAG  ... CENSTATE  CENCTY   FIPS  FIPS2
1             01     001  11----          ...       63       1  01001  01001
10            01     001  21----          ...       63       1  01001  01001
17            01     001  22----          ...       63       1  01001  01001
27            01     001  23----          ...       63       1  01001  01001
93            01     001  31----          ...       63       1  01001  01001
163           01     001  42----          ...       63       1  01001  01001
218           01     001  44----          ...       63       1  01001  01001
351           01     001  48----          ...       63       1  01001  01001
381           01     001  51----          ...       63       1  01001  01001
401           01     001  52----          ...       63       1  01001  01001
429           01     001  53----          ...       63       1  01001  01001
465           01     001  54----          ...       63       1  01001  01001
509           01     001  55----       A  ...       63       1  01001  01001
514           01     001  56----          ...       63       1  01001  01001
540           01     001  61----          ...       63       1  01001  01001
554           01     001  62----          ...       63       1  01001  01001
615           01     001  71----          ...       63       1  01001  01001
632           01     001  72----          ...       63       1  01001  01001
652           01     001  81----          ...       63       1  01001  01001
707           01     001  99----          ...       63       1  01001  01001
709           01     003  11----          ...       63       3  01003  01003
731           01     003  21----          ...       63       3  01003  01003
747           01     003  22----          ...       63       3  01003  01003
762           01     003  23----          ...       63       3  01003  01003
828           01     003  31----          ...       63       3  01003  01003
1052          01     003  42----          ...       63       3  01003  01003
1191          01     003  44----          ...       63       3  01003  01003
1353          01     003  48----          ...       63       3  01003  01003
1429          01     003  51----          ...       63       3  01003  01003
1471          01     003  52----          ...       63       3  01003  01003
...          ...     ...     ...     ...  ...      ...     ...    ...    ...
2126060       56     045  31----       C  ...       83      45  56045  56045
2126077       56     045  42----          ...       83      45  56045  56045
2126098       56     045  44----          ...       83      45  56045  56045
2126163       56     045  48----          ...       83      45  56045  56045
2126184       56     045  51----          ...       83      45  56045  56045
2126197       56     045  52----          ...       83      45  56045  56045
2126215       56     045  53----          ...       83      45  56045  56045
2126229       56     045  54----          ...       83      45  56045  56045
2126256       56     045  55----       A  ...       83      45  56045  56045
2126261       56     045  56----          ...       83      45  56045  56045
2126278       56     045  61----       A  ...       83      45  56045  56045
2126283       56     045  62----          ...       83      45  56045  56045
2126326       56     045  71----       A  ...       83      45  56045  56045
2126331       56     045  72----          ...       83      45  56045  56045
2126345       56     045  81----          ...       83      45  56045  56045
2126376       56     045  99----       A  ...       83      45  56045  56045
2126378       56     999  21----          ...       83     999  56999  56999
2126390       56     999  22----       A  ...       83     999  56999  56999
2126395       56     999  23----          ...       83     999  56999  56999
2126407       56     999  42----          ...       83     999  56999  56999
2126452       56     999  44----       A  ...       83     999  56999  56999
2126457       56     999  48----       E  ...       83     999  56999  56999
2126466       56     999  51----          ...       83     999  56999  56999
2126489       56     999  52----          ...       83     999  56999  56999
2126519       56     999  54----          ...       83     999  56999  56999
2126542       56     999  55----       A  ...       83     999  56999  56999
2126547       56     999  56----          ...       83     999  56999  56999
2126570       56     999  61----       A  ...       83     999  56999  56999
2126578       56     999  62----          ...       83     999  56999  56999
2126592       56     999  81----       A  ...       83     999  56999  56999

[58901 rows x 28 columns]
~~~
{:.output}


~~~python
cbp3.shape
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(58901, 27)
~~~
{:.output}


~~~python
cbp3.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
   FIPSTATE FIPSCTY   NAICS EMPFLAG  ... N1000_4  CENSTATE CENCTY   FIPS
1        01     001  11----          ...       0        63      1  01001
10       01     001  21----          ...       0        63      1  01001
17       01     001  22----          ...       0        63      1  01001
27       01     001  23----          ...       0        63      1  01001
93       01     001  31----          ...       0        63      1  01001

[5 rows x 27 columns]
~~~
{:.output}


===

### Select

To keep particular columns of a data frame (rather than filtering rows), use the
`filter` or `[ ]` functions with arguments that match column names.



~~~python
cbp2.columns
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
Index(['FIPSTATE', 'FIPSCTY', 'NAICS', 'EMPFLAG', 'EMP_NF', 'EMP', 'QP1_NF',
       'QP1', 'AP_NF', 'AP', 'EST', 'N1_4', 'N5_9', 'N10_19', 'N20_49',
       'N50_99', 'N100_249', 'N250_499', 'N500_999', 'N1000', 'N1000_1',
       'N1000_2', 'N1000_3', 'N1000_4', 'CENSTATE', 'CENCTY'],
      dtype='object')
~~~
{:.output}


===

One way to "match" is by including complete names, each one you want to keep:



~~~python
cbp3 = cbp3[['FIPS','NAICS','N1_4', 'N5_9', 'N10_19']] 
cbp3.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
     FIPS   NAICS  N1_4  N5_9  N10_19
1   01001  11----     5     1       0
10  01001  21----     0     1       1
17  01001  22----     2     1       2
27  01001  23----    51    13       7
93  01001  31----     9     4       4
~~~
{:.output}


===

Alternatively, we can use the `filter` function to select all columns starting with N or matching with 'FIPS' or 'NAICS' pattern. The `filter` command is useful when chaining methods (or piping operations). 



~~~python
cbp4= cbp.filter(regex='^N|FIPS|NAICS',axis=1) 
cbp4.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
  FIPSTATE FIPSCTY   NAICS  N1_4  ...  N1000_1  N1000_2  N1000_3  N1000_4
0       01     001  ------   430  ...        0        0        0        0
1       01     001  11----     5  ...        0        0        0        0
2       01     001  113///     4  ...        0        0        0        0
3       01     001  1133//     4  ...        0        0        0        0
4       01     001  11331/     4  ...        0        0        0        0

[5 rows x 16 columns]
~~~
{:.output}


