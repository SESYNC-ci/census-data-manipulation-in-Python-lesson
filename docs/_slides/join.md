---
excerpt: Join and Summarize
---

## Join

The CBP dataset uses FIPS to identify U.S. counties and NAICS codes to identify
types of industry. The ACS dataset also uses FIPS but their data may aggregate
across multiple NAICS codes representing a single industry sector.

===



~~~python
#sector <- fread(
#  'data/ACS/sector_naics.csv',
#  colClasses = c(NAICS='character'))
#sector =  pd.read_csv(
#  'data/ACS/sector_naics.csv',
#  dtype = {"NAICS": np.str})
sector =  pd.read_csv(
  '/nfs/public-data/training/ACS/sector_naics.csv',
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
+ #print(sector.shape)
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



~~~python

#cbp = pd.read_csv(
#  'data/cbp15co.csv',
 # na_values = "NULL",
 # keep_default_na=False,
#  dtype = {"FIPSTATE": np.str, "FIPSCTY": np.str}
#  )

#cbp <- cbp %>%
#  inner_join(sector)
#cbp.head()
logical_idx = cbp['NAICS'].str.match('[0-9]{2}----')
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

#print(cbp.NAICS)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(58901, 26)
~~~
{:.output}


~~~python
cbp['NAICS']= cbp.NAICS.apply(lambda x: np.int64(x[0:2]))
#print(cbp['NAICS'][0:1][0:2])
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}




~~~python
> #Many to one to join economic sector code to NAICS
+ print
~~~
{:title="Console" .input}


~~~
<built-in function print>
~~~
{:.output}


~~~python
> cbp_test = cbp.merge(sector, on = "NAICS")
+ print(cbp_test.shape)
~~~
{:title="Console" .input}


~~~
(56704, 27)
~~~
{:.output}


~~~python
> cbp_test.head()
~~~
{:title="Console" .input}


~~~
  FIPSTATE FIPSCTY  ...  CENCTY                                    Sector
0       01     001  ...       1  agriculture forestry fishing and hunting
1       01     003  ...       3  agriculture forestry fishing and hunting
2       01     005  ...       5  agriculture forestry fishing and hunting
3       01     007  ...       7  agriculture forestry fishing and hunting
4       01     009  ...       9  agriculture forestry fishing and hunting

[5 rows x 27 columns]
~~~
{:.output}


~~~python
> print(cbp_test.shape)
~~~
{:title="Console" .input}


~~~
(56704, 27)
~~~
{:.output}


~~~python
> cbp_test2 = cbp.merge(sector, on = "NAICS", how='inner')
+ cbp_test2.head()
~~~
{:title="Console" .input}


~~~
  FIPSTATE FIPSCTY  ...  CENCTY                                    Sector
0       01     001  ...       1  agriculture forestry fishing and hunting
1       01     003  ...       3  agriculture forestry fishing and hunting
2       01     005  ...       5  agriculture forestry fishing and hunting
3       01     007  ...       7  agriculture forestry fishing and hunting
4       01     009  ...       9  agriculture forestry fishing and hunting

[5 rows x 27 columns]
~~~
{:.output}


~~~python
> print(cbp_test2.shape)
~~~
{:title="Console" .input}


~~~
(56704, 27)
~~~
{:.output}


===

![]({% include asset.html path="images/many-to-one.svg" %}){:width="80%"}

The NAICS field in the `cbp` table can have the same value multiple times, it is
not a primary key in this table. In the `sector` table, the NAICS field is the
primary key uniquely identifying each record. The type of relationship between
these tables is therefore "many-to-one".
{:.notes}

===

Question
: Note that we lost a couple thousand rows through this join. How could
`cbp` have fewer rows after a join on NAICS codes?

Answer
: {:.fragment} The CBP data contains an NAICS code not mapped to a sector---the
"error code" 99 is not present in `sector`. The use of "error codes" that
could easilly be mistaken for data is frowned upon.

===

## Group By

A very common data manipulation procedure know as "split-apply-combine" tackles
the problem of applying the same transformation to subsets of data while keeping
the result all together. We need the total number of establishments
in each size class *aggregated within* each county and industry sector.

===

The pandas function `group_by` begins the process by indicating how the data
frame should be split into subsets.



~~~python
#cbp_grouped <- cbp %>%
#  group_by(FIPS, Sector)

#cbp["FIPSTATE"]= cbp["FIPSTATE"].astype(str)
#cbp["FIPSCTY"]= cbp["FIPSCTY"].astype(str)

cbp["FIPS"] = cbp["FIPSTATE"]+cbp["FIPSCTY"]

cbp_test = cbp.merge(sector, on = "NAICS")
#print(cbp_test.shape)
#cbp_test.head()
#cbp_test.columns

cbp_grouped = cbp_test.groupby(['FIPS','Sector'])
#cbp_grouped = cbp.groupby(['Sector'])

cbp_grouped

~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
<pandas.core.groupby.generic.DataFrameGroupBy object at 0x7f41c4c7ea90>
~~~
{:.output}


===

At this point, nothing has really changed:



~~~python
> #str(cbp_grouped)
+ cbp_grouped.dtypes
~~~
{:title="Console" .input}


~~~
                                                         FIPSTATE  ... CENCTY
FIPS  Sector                                                       ...       
01001 accommodation and food services                      object  ...  int64
      administrative and support and waste management...   object  ...  int64
      agriculture forestry fishing and hunting             object  ...  int64
      arts entertainment and recreation                    object  ...  int64
      construction                                         object  ...  int64
      educational services                                 object  ...  int64
      finance and insurance                                object  ...  int64
      health care and social assistance                    object  ...  int64
      information                                          object  ...  int64
      management of companies and enterprises              object  ...  int64
      manufacturing                                        object  ...  int64
      mining quarrying and oil and gas extraction          object  ...  int64
      other services except public administration          object  ...  int64
      professional scientific and technical services       object  ...  int64
      real estate and rental and leasing                   object  ...  int64
      retail trade                                         object  ...  int64
      transportation and warehousing                       object  ...  int64
      utilities                                            object  ...  int64
      wholesale trade                                      object  ...  int64
01003 accommodation and food services                      object  ...  int64
      administrative and support and waste management...   object  ...  int64
      agriculture forestry fishing and hunting             object  ...  int64
      arts entertainment and recreation                    object  ...  int64
      construction                                         object  ...  int64
      educational services                                 object  ...  int64
      finance and insurance                                object  ...  int64
      health care and social assistance                    object  ...  int64
      information                                          object  ...  int64
      management of companies and enterprises              object  ...  int64
      manufacturing                                        object  ...  int64
...                                                           ...  ...    ...
56045 arts entertainment and recreation                    object  ...  int64
      construction                                         object  ...  int64
      educational services                                 object  ...  int64
      finance and insurance                                object  ...  int64
      health care and social assistance                    object  ...  int64
      information                                          object  ...  int64
      management of companies and enterprises              object  ...  int64
      manufacturing                                        object  ...  int64
      mining quarrying and oil and gas extraction          object  ...  int64
      other services except public administration          object  ...  int64
      professional scientific and technical services       object  ...  int64
      real estate and rental and leasing                   object  ...  int64
      retail trade                                         object  ...  int64
      transportation and warehousing                       object  ...  int64
      utilities                                            object  ...  int64
      wholesale trade                                      object  ...  int64
56999 administrative and support and waste management...   object  ...  int64
      construction                                         object  ...  int64
      educational services                                 object  ...  int64
      finance and insurance                                object  ...  int64
      health care and social assistance                    object  ...  int64
      information                                          object  ...  int64
      management of companies and enterprises              object  ...  int64
      mining quarrying and oil and gas extraction          object  ...  int64
      other services except public administration          object  ...  int64
      professional scientific and technical services       object  ...  int64
      retail trade                                         object  ...  int64
      transportation and warehousing                       object  ...  int64
      utilities                                            object  ...  int64
      wholesale trade                                      object  ...  int64

[56704 rows x 26 columns]
~~~
{:.output}


The `group_by` statement does not change any values in the data frame; it only
adds attributes to the the original data frame. You can add multiple variables
(separated by commas) in `group_by`; each distinct combination of values across
these columns defines a different group.
{:.notes}

===

## Summarize

The operation to perform on each group is summing: we need to sum the number of
establishments in each group. Using [dplyr](){:.rlib} functions, the summaries
are automically combined into a data frame.

===



~~~python
#cbp <- cbp %>%
#  group_by(FIPS, Sector) %>%
#  select(starts_with('N'), -NAICS) %>%
#  summarize_all(sum)

test43 = (cbp_test.groupby(['FIPS', 'Sector']) 
                  .agg('sum')
                  .filter(regex='^N')
                  .drop(columns=['NAICS']))
test43.head(5)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
                                                          N1_4  ...  N1000_4
FIPS  Sector                                                    ...         
01001 accommodation and food services                       23  ...        0
      administrative and support and waste management...    18  ...        0
      agriculture forestry fishing and hunting               5  ...        0
      arts entertainment and recreation                      5  ...        0
      construction                                          51  ...        0

[5 rows x 13 columns]
~~~
{:.output}


The "combine" part of "split-apply-combine" occurs automatically, when the
attributes introduced by `groupby` are dropped. You can see attributes 
by running the `dtypes` function on the data frame.
{:.notes}

===

![]({% include asset.html path="images/one-to-one.svg" %}){:width="80%"}

There is now a one-to-one relationship between `cbp` and `acs`, based on
the combination of FIPS and Sector as the primary key for both tables.
{:.notes}

===



~~~python
#acs_cbp <- cbp %>%
#  inner_join(acs)
#acs_cbp = test43.merge(acs)
#print(acs_cbp.shape)
print(test43.shape)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(56704, 13)
~~~
{:.output}


~~~python
print(acs.shape)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(59698, 4)
~~~
{:.output}


~~~python
acs_cbp = test43.merge(acs,on='FIPS')
print(acs_cbp.shape)
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


~~~
(1061416, 17)
~~~
{:.output}


Again, however, the one-to-one relationship does not mean all rows are preserved
by the join. The specific nature of the `inner_join` is to keep all rows, even
duplicating rows if the relationship is many-to-one, where there are matching
values in both tables, and discarding the rest.
{:.notes}

===

The `acs_cbp` table now includes the `median_income` variable from the ACS and
appropriatey aggregated establishment size information (the number of
establishments by employee bins) from the CBP table.



~~~python
> acs_cbp
~~~
{:title="Console" .no-eval .input}

