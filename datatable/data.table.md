data.table for R
========================================================
author: David Whitney 
date: 21 July, 2014

Acknowledgements/Resources
========================================================
I first learned data.table from the materials at

- https://github.com/raphg/Biostat-578

- http://cran.r-project.org/web/packages/data.table/vignettes/datatable-intro.pdf

- http://datatable.r-forge.r-project.org/datatable-faq.pdf

R data structures
========================================================
Vectors, Matrices and Arrays allow storage of a single class of element (integer, double, etc.). Data.frames and lists allow greater flexibility, with data.frames being the go-to for most data analyses in R.

data.table
========================================================
Data.table is an R package that inherits the data.frame class and adds new functionality. One of the goals in writing the package was to make writing/reading code easier.


```r
data(cars)
library("data.table")
(dt <- data.table(cars))
```

```
    speed dist
 1:     4    2
 2:     4   10
 3:     7    4
 4:     7   22
 5:     8   16
 6:     9   10
 7:    10   18
 8:    10   26
 9:    10   34
10:    11   17
11:    11   28
12:    12   14
13:    12   20
14:    12   24
15:    12   28
16:    13   26
17:    13   34
18:    13   34
19:    13   46
20:    14   26
21:    14   36
22:    14   60
23:    14   80
24:    15   20
25:    15   26
26:    15   54
27:    16   32
28:    16   40
29:    17   32
30:    17   40
31:    17   50
32:    18   42
33:    18   56
34:    18   76
35:    18   84
36:    19   36
37:    19   46
38:    19   68
39:    20   32
40:    20   48
41:    20   52
42:    20   56
43:    20   64
44:    22   66
45:    23   54
46:    24   70
47:    24   92
48:    24   93
49:    24  120
50:    25   85
    speed dist
```



Filtering rows: data.frame
========================================================
There are several ways to subset a data.frame. All of them look like some variant of:

```r
cars[with( cars, speed >17 & dist <60 ),]
```

```
   speed dist
32    18   42
33    18   56
36    19   36
37    19   46
39    20   32
40    20   48
41    20   52
42    20   56
45    23   54
```


Filtering rows: data.table and "i"
========================================================
For a data.table, filtering can be done without accessing a column by car$speed or the with() function. The following syntax is calling the "i" function which essentially creates a temporary environment where the columns are treated as local variables.

```r
dt[speed >17 & dist <60,]
```

```
   speed dist
1:    18   42
2:    18   56
3:    19   36
4:    19   46
5:    20   32
6:    20   48
7:    20   52
8:    20   56
9:    23   54
```


Column computations: data.able and "j"
=========================================================
Similar to the "i" function for rows, we can do column-wise computations with what is called the "j" function.

```r
dt[,dist/speed]
```

```
 [1] 0.5000 2.5000 0.5714 3.1429 2.0000 1.1111 1.8000 2.6000 3.4000 1.5455
[11] 2.5455 1.1667 1.6667 2.0000 2.3333 2.0000 2.6154 2.6154 3.5385 1.8571
[21] 2.5714 4.2857 5.7143 1.3333 1.7333 3.6000 2.0000 2.5000 1.8824 2.3529
[31] 2.9412 2.3333 3.1111 4.2222 4.6667 1.8947 2.4211 3.5789 1.6000 2.4000
[41] 2.6000 2.8000 3.2000 3.0000 2.3478 2.9167 3.8333 3.8750 5.0000 3.4000
```


Column updates I
==========================================================
We may want to keep newly computed values instead of just displaying them. No problem! Use the symbol ":=" in your above call to "j" to define a new column at the end of your table:

```r
dt[,time:=dist/speed]
```

```
    speed dist   time
 1:     4    2 0.5000
 2:     4   10 2.5000
 3:     7    4 0.5714
 4:     7   22 3.1429
 5:     8   16 2.0000
 6:     9   10 1.1111
 7:    10   18 1.8000
 8:    10   26 2.6000
 9:    10   34 3.4000
10:    11   17 1.5455
11:    11   28 2.5455
12:    12   14 1.1667
13:    12   20 1.6667
14:    12   24 2.0000
15:    12   28 2.3333
16:    13   26 2.0000
17:    13   34 2.6154
18:    13   34 2.6154
19:    13   46 3.5385
20:    14   26 1.8571
21:    14   36 2.5714
22:    14   60 4.2857
23:    14   80 5.7143
24:    15   20 1.3333
25:    15   26 1.7333
26:    15   54 3.6000
27:    16   32 2.0000
28:    16   40 2.5000
29:    17   32 1.8824
30:    17   40 2.3529
31:    17   50 2.9412
32:    18   42 2.3333
33:    18   56 3.1111
34:    18   76 4.2222
35:    18   84 4.6667
36:    19   36 1.8947
37:    19   46 2.4211
38:    19   68 3.5789
39:    20   32 1.6000
40:    20   48 2.4000
41:    20   52 2.6000
42:    20   56 2.8000
43:    20   64 3.2000
44:    22   66 3.0000
45:    23   54 2.3478
46:    24   70 2.9167
47:    24   92 3.8333
48:    24   93 3.8750
49:    24  120 5.0000
50:    25   85 3.4000
    speed dist   time
```


Column updates II
==========================================================
We can reorder the columns in a data.table by

```r
(setcolorder(dt,c("dist","time","speed")))
```

```
    dist   time speed
 1:    2 0.5000     4
 2:   10 2.5000     4
 3:    4 0.5714     7
 4:   22 3.1429     7
 5:   16 2.0000     8
 6:   10 1.1111     9
 7:   18 1.8000    10
 8:   26 2.6000    10
 9:   34 3.4000    10
10:   17 1.5455    11
11:   28 2.5455    11
12:   14 1.1667    12
13:   20 1.6667    12
14:   24 2.0000    12
15:   28 2.3333    12
16:   26 2.0000    13
17:   34 2.6154    13
18:   34 2.6154    13
19:   46 3.5385    13
20:   26 1.8571    14
21:   36 2.5714    14
22:   60 4.2857    14
23:   80 5.7143    14
24:   20 1.3333    15
25:   26 1.7333    15
26:   54 3.6000    15
27:   32 2.0000    16
28:   40 2.5000    16
29:   32 1.8824    17
30:   40 2.3529    17
31:   50 2.9412    17
32:   42 2.3333    18
33:   56 3.1111    18
34:   76 4.2222    18
35:   84 4.6667    18
36:   36 1.8947    19
37:   46 2.4211    19
38:   68 3.5789    19
39:   32 1.6000    20
40:   48 2.4000    20
41:   52 2.6000    20
42:   56 2.8000    20
43:   64 3.2000    20
44:   66 3.0000    22
45:   54 2.3478    23
46:   70 2.9167    24
47:   92 3.8333    24
48:   93 3.8750    24
49:  120 5.0000    24
50:   85 3.4000    25
    dist   time speed
```


Column updates III
==========================================================
We can delete a column by setting it to NULL.

```r
dt[,time:=NULL]
```

```
    dist speed
 1:    2     4
 2:   10     4
 3:    4     7
 4:   22     7
 5:   16     8
 6:   10     9
 7:   18    10
 8:   26    10
 9:   34    10
10:   17    11
11:   28    11
12:   14    12
13:   20    12
14:   24    12
15:   28    12
16:   26    13
17:   34    13
18:   34    13
19:   46    13
20:   26    14
21:   36    14
22:   60    14
23:   80    14
24:   20    15
25:   26    15
26:   54    15
27:   32    16
28:   40    16
29:   32    17
30:   40    17
31:   50    17
32:   42    18
33:   56    18
34:   76    18
35:   84    18
36:   36    19
37:   46    19
38:   68    19
39:   32    20
40:   48    20
41:   52    20
42:   56    20
43:   64    20
44:   66    22
45:   54    23
46:   70    24
47:   92    24
48:   93    24
49:  120    24
50:   85    25
    dist speed
```


By
==========================================================
If you would like to cluster observations by values in a certain column, you can include a third argument. This is similar to using tapply() to apply a function by a factor(s). 


```r
dt[,mean(dist),by=speed]
```

```
    speed    V1
 1:     4  6.00
 2:     7 13.00
 3:     8 16.00
 4:     9 10.00
 5:    10 26.00
 6:    11 22.50
 7:    12 21.50
 8:    13 35.00
 9:    14 50.50
10:    15 33.33
11:    16 36.00
12:    17 40.67
13:    18 64.50
14:    19 50.00
15:    20 50.40
16:    22 66.00
17:    23 54.00
18:    24 93.75
19:    25 85.00
```


By, by
==========================================================
Note that it is not strictly necessary to create a new column when you do this, though it is possible. In fact, you can create several new columns at once by passing a vector of names and a list of new columns.

```r
dt[,c("iMeanIt","SD"):=list(mean(dist),sd(dist)),by=speed]
```

```
    dist speed iMeanIt     SD
 1:    2     4    6.00  5.657
 2:   10     4    6.00  5.657
 3:    4     7   13.00 12.728
 4:   22     7   13.00 12.728
 5:   16     8   16.00     NA
 6:   10     9   10.00     NA
 7:   18    10   26.00  8.000
 8:   26    10   26.00  8.000
 9:   34    10   26.00  8.000
10:   17    11   22.50  7.778
11:   28    11   22.50  7.778
12:   14    12   21.50  5.972
13:   20    12   21.50  5.972
14:   24    12   21.50  5.972
15:   28    12   21.50  5.972
16:   26    13   35.00  8.246
17:   34    13   35.00  8.246
18:   34    13   35.00  8.246
19:   46    13   35.00  8.246
20:   26    14   50.50 24.297
21:   36    14   50.50 24.297
22:   60    14   50.50 24.297
23:   80    14   50.50 24.297
24:   20    15   33.33 18.148
25:   26    15   33.33 18.148
26:   54    15   33.33 18.148
27:   32    16   36.00  5.657
28:   40    16   36.00  5.657
29:   32    17   40.67  9.018
30:   40    17   40.67  9.018
31:   50    17   40.67  9.018
32:   42    18   64.50 19.070
33:   56    18   64.50 19.070
34:   76    18   64.50 19.070
35:   84    18   64.50 19.070
36:   36    19   50.00 16.371
37:   46    19   50.00 16.371
38:   68    19   50.00 16.371
39:   32    20   50.40 11.866
40:   48    20   50.40 11.866
41:   52    20   50.40 11.866
42:   56    20   50.40 11.866
43:   64    20   50.40 11.866
44:   66    22   66.00     NA
45:   54    23   54.00     NA
46:   70    24   93.75 20.467
47:   92    24   93.75 20.467
48:   93    24   93.75 20.467
49:  120    24   93.75 20.467
50:   85    25   85.00     NA
    dist speed iMeanIt     SD
```


Memory
==========================================================
We haven't been using the assignment operators like "<-" and "=". The reason for this is that data.table is written to update currently allocated memory and avoid copying large units of memory.

Working with memory
==========================================================
Because of the way data.table handles memory, many updates we perform on data.frames are handled slightly differently. Failing to do so will usually merit a warning from R, but can result in a segfault.

```r
#names(dt) <- c("do","not","use","this")
oldies <- names(dt)
goodies <- c("these","are","better","names")
setnames(dt, oldies, goodies)
```

Another example of this was setcolorder().

The key to speed
=========================================================
Another important function to use with data.table is setkey(). When we set a key, we are specifying a column to sort the data.table by. This also allows us to perform joins on multiple tables sharing the same key.


```r
dt1 <- as.data.table(cars)
dt2 <- copy(dt1) #so we don't change dt1 as well!
invisible(dt2[,meanbyd:=mean(speed),by=dist])
setkey(dt1,dist)
setkey(dt2,dist)
```

Note: without using copy() first, we would be accessing the same memory with both dt1 and dt2!

Joins
=========================================================

```r
head(dt1)
```

```
   speed dist
1:     4    2
2:     7    4
3:     4   10
4:     9   10
5:    12   14
6:     8   16
```

```r
head(dt1[,dt2])
```

```
   speed dist meanbyd
1:     4    2     4.0
2:     7    4     7.0
3:     4   10     6.5
4:     9   10     6.5
5:    12   14    12.0
6:     8   16     8.0
```


More
=========================================================

- data.table uses binary search instead of a vector scan to find the target elements for joins or "by"

- hence we can write concise code that is also significantly faster than equivalent methods for data.frames

- data.tables inherit from data.frame and are compatible with any function that takes data.frame input only

Example usage
=========================================================

(on pearson)
