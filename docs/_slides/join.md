---
excerpt: Join and Summarize
---

## Join

The CBP dataset uses FIPS to identify U.S. counties and NAICS codes to identify
types of industry. The ACS dataset also uses FIPS but their data may aggregate
across multiple NAICS codes representing a single industry sector.

===



~~~python

sector =  pd.read_csv(
  'data/ACS/sector_naics.csv',
  dtype = {"NAICS": np.int64})
print(sector.dtypes)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
Sector    object
NAICS      int64
dtype: object
~~~
{:.output}


~~~python
print(cbp.dtypes)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
FIPSTATE    object
FIPSCTY     object
NAICS       object
EMPFLAG     object
EMP_NF      object
EMP          int64
QP1_NF      object
QP1          int64
AP_NF       object
AP           int64
EST          int64
N1_4         int64
N5_9         int64
N10_19       int64
N20_49       int64
N50_99       int64
N100_249     int64
N250_499     int64
N500_999     int64
N1000        int64
N1000_1      int64
N1000_2      int64
N1000_3      int64
N1000_4      int64
CENSTATE     int64
CENCTY       int64
dtype: object
~~~
{:.output}


~~~python
cbp.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
  FIPSTATE FIPSCTY   NAICS EMPFLAG  ... N1000_3  N1000_4 CENSTATE  CENCTY
0       01     001  ------          ...       0        0       63       1
1       01     001  11----          ...       0        0       63       1
2       01     001  113///          ...       0        0       63       1
3       01     001  1133//          ...       0        0       63       1
4       01     001  11331/          ...       0        0       63       1

[5 rows x 26 columns]
~~~
{:.output}


~~~python
cbp.dtypes
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
FIPSTATE    object
FIPSCTY     object
NAICS       object
EMPFLAG     object
EMP_NF      object
EMP          int64
QP1_NF      object
QP1          int64
AP_NF       object
AP           int64
EST          int64
N1_4         int64
N5_9         int64
N10_19       int64
N20_49       int64
N50_99       int64
N100_249     int64
N250_499     int64
N500_999     int64
N1000        int64
N1000_1      int64
N1000_2      int64
N1000_3      int64
N1000_4      int64
CENSTATE     int64
CENCTY       int64
dtype: object
~~~
{:.output}


~~~python
cbp.head()
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
  FIPSTATE FIPSCTY   NAICS EMPFLAG  ... N1000_3  N1000_4 CENSTATE  CENCTY
0       01     001  ------          ...       0        0       63       1
1       01     001  11----          ...       0        0       63       1
2       01     001  113///          ...       0        0       63       1
3       01     001  1133//          ...       0        0       63       1
4       01     001  11331/          ...       0        0       63       1

[5 rows x 26 columns]
~~~
{:.output}



~~~python
> print(sector.dtypes)
~~~
{:title="Console" .input}


~~~
Sector    object
NAICS      int64
dtype: object
~~~
{:.output}


~~~python
> print(sector.shape) #24 economic sectors
~~~
{:title="Console" .input}


~~~
(24, 2)
~~~
{:.output}


~~~python
> sector.head()
~~~
{:title="Console" .input}


~~~
                                        Sector  NAICS
0     agriculture forestry fishing and hunting     11
1  mining quarrying and oil and gas extraction     21
2                                    utilities     22
3                                 construction     23
4                                manufacturing     31
~~~
{:.output}


Probably the primary challenge in combining secondary datasets for synthesis
research is dealing with their different sampling frames. A very common issue is
that data are collected at different "scales", with one dataset being at higher
spatial or temporal resolution than another. The differences between the CBP and
ACS categories of industry present a similar problem, and require the same
solution of re-aggregating data at the "lower resolution".
{:.notes}

===

### Many-to-One

Before performing the join operation, some preprocessing is necessary to extract from the NAICS columns the first two digits matching the sector identifiers.



~~~python

logical_idx = cbp['NAICS'].str.match('[0-9]{2}----') #boolean index
cbp = cbp.loc[logical_idx]
cbp.head()
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


~~~python
cbp.shape
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(58901, 26)
~~~
{:.output}


~~~python
cbp['NAICS']= cbp.NAICS.apply(lambda x: np.int64(x[0:2])) # select first two digits
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}













