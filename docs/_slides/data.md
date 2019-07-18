---
# empflag for NA strings
#
---

## Sample Data

![]({% include asset.html path="images/data.jpg" %}){: width="50%"}  
*Credit: [US Census Bureau](https://www.census.gov/programs-surveys/cbp.html)*
{:.captioned}

===

To learn about data transformation with pandas, we need more data. The Census
Bureau collects subnational economic data for the U.S., releasing annual [County
Business Patterns (CBP)] datasets including the number of establishments,
employment, and payroll by industry. They also conduct the [American Community
Survey (ACS)] and publish, among other demographic and economic variables, estimates of
median income for individuals working in different industries.
{:.notes}

- [County Business Patterns (CBP)]
- [American Community Survey (ACS)]

[County Business Patterns (CBP)]: https://www.census.gov/programs-surveys/cbp/data/datasets.html
[American Community Survey (ACS)]: https://www.census.gov/programs-surveys/acs/

===



~~~python
> import pandas as pd
+ cbp = pd.read_csv('data/cbp15co.csv')
+ cbp.describe()
~~~
{:title="Console" .input}



~~~python
> print(cbp.dtypes)
~~~
{:title="Console" .input}


~~~
FIPSTATE     int64
FIPSCTY      int64
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


See the [CBP dataset documentation] for an explanation of the variables we don't
discuss in this lesson.
{:.notes}

[CBP dataset documentation]: https://www2.census.gov/programs-surveys/rhfs/cbp/technical%20documentation/2015_record_layouts/county_layout_2015.txt

===

Modify the import to clean up this read: consider the data type for FIPS codes
along with what string in this CSV file represents NAs, a.k.a. data that is
not-available or missing.



~~~python

import numpy as np
import pandas as pd

cbp = pd.read_csv(
  'data/cbp15co.csv',
  na_values = "NULL",
  keep_default_na=False,
  dtype =  {"FIPSTATE": np.str, 
  "FIPSCTY": np.str}
  )
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}


===

Question
: What changed?

Answer
: {:.fragment} Using `dtypes()` shows that the character string `""` in the CSV
file is no longer read into R as missing data (an `NA`) but as an empty string.
The two named "FIPS" columns are now correctly read as strings.

===



~~~python
import pandas as pd
import numpy as np
acs =  pd.read_csv(
  'data/ACS/sector_ACS_15_5YR_S2413.csv',
  dtype = {"FIPS": np.str}
  )
~~~
{:title="{{ site.data.lesson.handouts[0] }}" .text-document}

Now let's display the data types


~~~python
> #acs.dtypes
+ print(acs.dtypes)
~~~
{:title="Console" .input}


~~~
FIPS              object
County            object
Sector            object
median_income    float64
dtype: object
~~~
{:.output}


===

The two datasets both contain economic variables for each U.S. county and
specified by different categories of industry. The data could potentially be
manipulated into a single table reflecting the follow statistical model.

$$
median\_income \sim industry + establishment\_size
$$
