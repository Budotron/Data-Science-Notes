---
title: "Data Types in R"
author: "Varun Boodram"
date: "September 21, 2014"
output: html_document
---

Everything that you encounter in R is called an "object". There are five basic classes of objects  

* character  
* numeric (real or decimals)
* integer
* complex numbers
* logicals(T/F)

### Vectors
The most basic object in R is a *vector*. It can contain multiple copies of a single type of object; i.e: you can have a vector of characters, or a vector of integers. 
eg.

```r
v<-c(1,2,3,4)
v; class(v)
```

```
## [1] 1 2 3 4
```

```
## [1] "numeric"
```

```r
y<-c(1L,2L,3L,4L) #L indicates integers
y; class(y)
```

```
## [1] 1 2 3 4
```

```
## [1] "integer"
```

```r
b<-c("a", "b", "c", "Q")
b; class(b)
```

```
## [1] "a" "b" "c" "Q"
```

```
## [1] "character"
```
An empty vector is created by the vector function. 

```r
vector()
```

```
## logical(0)
```
The above function takes two arguments: the **class** of the vector (the types of objects that you want to have in the vector), and the **length** of the vector. 

We **cannot** have mixed types of objects in a vector. A *list* is represented as a vector, but each element of that vector can belong to a different class. Any element of a list can be anything, a data frame, another list...

```r
v<-c(2, "bass", 1L)
v; class(v)
```

```
## [1] "2"    "bass" "1"
```

```
## [1] "character"
```
With vectors of mixed objects, R will create a "least common denominator" vector. This is called **coersion**. Objects can be explicitly coerced with as. functions. Nonsensical coercions return NAs

```r
x<-0:6
class(x)
```

```
## [1] "integer"
```

```r
as.numeric(x)
```

```
## [1] 0 1 2 3 4 5 6
```

```r
as.logical(x)
```

```
## [1] FALSE  TRUE  TRUE  TRUE  TRUE  TRUE  TRUE
```

```r
as.character(x)
```

```
## [1] "0" "1" "2" "3" "4" "5" "6"
```
Note that these coercions do *not* affect the class of x, unless x is reassigned to the the coerced form

```r
class(x)
```

```
## [1] "integer"
```

```r
x<-as.numeric(x)
class(x)
```

```
## [1] "numeric"
```
**Matrices** are vectors with a *dimension* attribute. They are constructed columnwise, unless byrow=T

```r
matrix(data = 1:6, nrow = 2, ncol = 3, byrow = F)
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

```r
matrix(data = 1:6, nrow = 2, ncol = 3, byrow = T)
```

```
##      [,1] [,2] [,3]
## [1,]    1    2    3
## [2,]    4    5    6
```
matrices can be constructed from vectors by adding the dim attribute

```r
m<-1:10; m
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
dim(m)<-c(2, 5); m
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    1    3    5    7    9
## [2,]    2    4    6    8   10
```
**Arrays** are kinda like a list of matrices, and are the same insofar as they are vectors that have a dimension attribute applied to them. 

```r
x<-1:20; dim(x)<-c(2,2,5);x
```

```
## , , 1
## 
##      [,1] [,2]
## [1,]    1    3
## [2,]    2    4
## 
## , , 2
## 
##      [,1] [,2]
## [1,]    5    7
## [2,]    6    8
## 
## , , 3
## 
##      [,1] [,2]
## [1,]    9   11
## [2,]   10   12
## 
## , , 4
## 
##      [,1] [,2]
## [1,]   13   15
## [2,]   14   16
## 
## , , 5
## 
##      [,1] [,2]
## [1,]   17   19
## [2,]   18   20
```

### Exploring Lists
Lists are vector-like objects with elements from different classes. If we print a list, the appearance is unlike what we saw before, because every element is listed seperately. The elements are indexed by double brackets. Here the first element is a one dimensional vector 1, the second element is a one dimensional vector "a", the third element is a vector of length 3 (1,2,3)

```r
x<-list(1, "a", c(1,2,3), 1+4i)
x
```

```
## [[1]]
## [1] 1
## 
## [[2]]
## [1] "a"
## 
## [[3]]
## [1] 1 2 3
## 
## [[4]]
## [1] 1+4i
```

```r
#note x[[i]] returns the ith element of the list
x[[3]]
```

```
## [1] 1 2 3
```
### Numbers in R
All numbers in R are generally treated as numeric objects. The L suffix specifies that you want the number to be treated as an integer, which can be overridden if the number is not

```r
class(1)
```

```
## [1] "numeric"
```

```r
class(1L)
```

```
## [1] "integer"
```

```r
class(3.2L) #3.2L is coerced to numeric
```

```
## [1] "numeric"
```
Inf stands for infinity. This expression can be treated as a real number

```r
1/0
```

```
## [1] Inf
```

```r
1/Inf
```

```
## [1] 0
```
NaN stands for "not a number"; it is an undefined value, and can be thought of as a missing value

```r
0/0
```

```
## [1] NaN
```

```r
1/NaN
```

```
## [1] NaN
```

### Attributes
While not every object in R comes with attributes, attributes are often part of an object in R. 

```r
# the names of the dimensions of a data frame are an attribute
names(iris)
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
## [5] "Species"
```

```r
colnames(iris)
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
## [5] "Species"
```

```r
rownames(iris)
```

```
##   [1] "1"   "2"   "3"   "4"   "5"   "6"   "7"   "8"   "9"   "10"  "11" 
##  [12] "12"  "13"  "14"  "15"  "16"  "17"  "18"  "19"  "20"  "21"  "22" 
##  [23] "23"  "24"  "25"  "26"  "27"  "28"  "29"  "30"  "31"  "32"  "33" 
##  [34] "34"  "35"  "36"  "37"  "38"  "39"  "40"  "41"  "42"  "43"  "44" 
##  [45] "45"  "46"  "47"  "48"  "49"  "50"  "51"  "52"  "53"  "54"  "55" 
##  [56] "56"  "57"  "58"  "59"  "60"  "61"  "62"  "63"  "64"  "65"  "66" 
##  [67] "67"  "68"  "69"  "70"  "71"  "72"  "73"  "74"  "75"  "76"  "77" 
##  [78] "78"  "79"  "80"  "81"  "82"  "83"  "84"  "85"  "86"  "87"  "88" 
##  [89] "89"  "90"  "91"  "92"  "93"  "94"  "95"  "96"  "97"  "98"  "99" 
## [100] "100" "101" "102" "103" "104" "105" "106" "107" "108" "109" "110"
## [111] "111" "112" "113" "114" "115" "116" "117" "118" "119" "120" "121"
## [122] "122" "123" "124" "125" "126" "127" "128" "129" "130" "131" "132"
## [133] "133" "134" "135" "136" "137" "138" "139" "140" "141" "142" "143"
## [144] "144" "145" "146" "147" "148" "149" "150"
```

```r
dimnames(iris)
```

```
## [[1]]
##   [1] "1"   "2"   "3"   "4"   "5"   "6"   "7"   "8"   "9"   "10"  "11" 
##  [12] "12"  "13"  "14"  "15"  "16"  "17"  "18"  "19"  "20"  "21"  "22" 
##  [23] "23"  "24"  "25"  "26"  "27"  "28"  "29"  "30"  "31"  "32"  "33" 
##  [34] "34"  "35"  "36"  "37"  "38"  "39"  "40"  "41"  "42"  "43"  "44" 
##  [45] "45"  "46"  "47"  "48"  "49"  "50"  "51"  "52"  "53"  "54"  "55" 
##  [56] "56"  "57"  "58"  "59"  "60"  "61"  "62"  "63"  "64"  "65"  "66" 
##  [67] "67"  "68"  "69"  "70"  "71"  "72"  "73"  "74"  "75"  "76"  "77" 
##  [78] "78"  "79"  "80"  "81"  "82"  "83"  "84"  "85"  "86"  "87"  "88" 
##  [89] "89"  "90"  "91"  "92"  "93"  "94"  "95"  "96"  "97"  "98"  "99" 
## [100] "100" "101" "102" "103" "104" "105" "106" "107" "108" "109" "110"
## [111] "111" "112" "113" "114" "115" "116" "117" "118" "119" "120" "121"
## [122] "122" "123" "124" "125" "126" "127" "128" "129" "130" "131" "132"
## [133] "133" "134" "135" "136" "137" "138" "139" "140" "141" "142" "143"
## [144] "144" "145" "146" "147" "148" "149" "150"
## 
## [[2]]
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
## [5] "Species"
```

```r
#because dimnames() returns a list, we can explore the second element with
dimnames(iris)[2]
```

```
## [[1]]
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
## [5] "Species"
```

```r
# dimensions of a matrix or an array are an attribute. A multidimensional array will have more than two dimensions; matrices will have two; vectors, none
m<-matrix(1:10, nrow = 2, ncol = 5, byrow = F)
dim(m)
```

```
## [1] 2 5
```

```r
v<-c(1,2,4)
dim(v)
```

```
## NULL
```

```r
# the class of the object is an attribute
# every object also has a length
length(v)
```

```
## [1] 3
```

```r
length(m) 
```

```
## [1] 10
```

```r
# attributes can also be assigned by the user
class(v)
```

```
## [1] "numeric"
```

```r
v<-as.integer(v)
v; class(v)
```

```
## [1] 1 2 4
```

```
## [1] "integer"
```

```r
# attributes can be acsessed by, set, or modified by the attributes () function
attributes(v)
```

```
## NULL
```

```r
attributes(m)
```

```
## $dim
## [1] 2 5
```

```r
attributes(iris)
```

```
## $names
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
## [5] "Species"     
## 
## $row.names
##   [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17
##  [18]  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34
##  [35]  35  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51
##  [52]  52  53  54  55  56  57  58  59  60  61  62  63  64  65  66  67  68
##  [69]  69  70  71  72  73  74  75  76  77  78  79  80  81  82  83  84  85
##  [86]  86  87  88  89  90  91  92  93  94  95  96  97  98  99 100 101 102
## [103] 103 104 105 106 107 108 109 110 111 112 113 114 115 116 117 118 119
## [120] 120 121 122 123 124 125 126 127 128 129 130 131 132 133 134 135 136
## [137] 137 138 139 140 141 142 143 144 145 146 147 148 149 150
## 
## $class
## [1] "data.frame"
```
### Console
The > is called the R prompt; when we type, we enter *expressions*. <- is called an **assignment operator**. 

When an expression is entered at the prompt, it is evaluated, and the result returned. 

Printing is controlled explicitly by print(), or by entering the object into the prompt

```r
x<-1:20
print(x)
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
```

```r
x
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
```

```r
class(x)
```

```
## [1] "integer"
```

### Factors

A factor is a special kind of vector that is used to represent categorical data. There are two types of factors 

* unordered (eg. male and female)
* ordered (eg: ranked list, 1,2,3)
A factor can be thought of as an integer vector where each integer has a *label*. So, if the categories are "high", "med", and "low", these can be thought of as an integer vector (1,2,3), where 1="high", etc

Factors can be created using factor(). If the caegorical data is "male" and "female", and the cases are 

```r
x<-factor(c("male", "male", "female", "male", "female")); x
```

```
## [1] male   male   female male   female
## Levels: female male
```
the levels are the categories. table() gives a count of how many of each level there are

```r
table(x)
```

```
## x
## female   male 
##      2      3
```
To see how R interprets a factor vector, call unclass()

```r
x<-factor(c("male", "male", "female", "male", "female", "intersex"))
unclass(x)
```

```
## [1] 3 3 1 3 1 2
## attr(,"levels")
## [1] "female"   "intersex" "male"
```
Note that the levels are assigned alphabetically. This may not correspond to the baseline level, the first level in the factor. Right now, the baseline level is "female", because f comes before i in the alphabet. Use

```r
x<-factor(x = c("male", "male", "female", "male", "female", "intersex"), levels = c("male", "female", "intersex")); unclass(x)
```

```
## [1] 1 1 2 1 2 3
## attr(,"levels")
## [1] "male"     "female"   "intersex"
```
### Missing Values
Missing values are also objects. 

* If mathematical operations are undefined (eg, 0/0), we get NaN
* For everything else, we get NA. We can have missing numerica values, or missing character values...; this means that NA objects also have a class
* NaNs are NAs, but not vice-versa

```r
x<-c(1, 2, NaN, NA, 4)
is.na(x)
```

```
## [1] FALSE FALSE  TRUE  TRUE FALSE
```

```r
is.nan(x)
```

```
## [1] FALSE FALSE  TRUE FALSE FALSE
```
### _Data Frames_
This is a key data type used in R. They are used to store tabular data. They are a special type of list, where every element of that list has the same length. 

* each column of the data frame is a list; each list has the same length

However, each column doesn't have to be the same type. The first column can be numbers, the second, factors (categorical data), etc.   
A matrix must store the same type of object in every element of the matrix. Data frames are distinguished from matrices by the fact that they can store different classes of objects.  
$row.names is an attribute characteristic of data frames. 
Data frames are created with 

* read.table() or
* read.csv()

Data fames can be converted into a matrix with the data.matrix() function. However, because all elements of the data frame are coerced into the same kind of object, the result may not be what you want or expected

Steps in manually creating a data frame

1. create lists of equal length
2. combine those lists with data.frame()

```r
# two line example
foo<-1:4
bar<-c("a", "b","c", "d")
x<-data.frame(foo, bar); x
```

```
##   foo bar
## 1   1   a
## 2   2   b
## 3   3   c
## 4   4   d
```

```r
# one line example
x<-data.frame(foo= 2:5, bar=c(T,F,T,T)); x
```

```
##   foo   bar
## 1   2  TRUE
## 2   3 FALSE
## 3   4  TRUE
## 4   5  TRUE
```

```r
attributes(x)
```

```
## $names
## [1] "foo" "bar"
## 
## $row.names
## [1] 1 2 3 4
## 
## $class
## [1] "data.frame"
```

### Names

All R objects can have names (not just data frames). This can be useful for writing 

* readable code
* self-describing objects

for example, we can name each element of the vector X

```r
x<-1:3
x
```

```
## [1] 1 2 3
```

```r
names(x)
```

```
## NULL
```

```r
names(x)<-c("foo", "bar", "norf")
names(x)
```

```
## [1] "foo"  "bar"  "norf"
```

```r
x
```

```
##  foo  bar norf 
##    1    2    3
```

or lists can have names

```r
x<-list(a=1, b=2, c=3)
x 
```

```
## $a
## [1] 1
## 
## $b
## [1] 2
## 
## $c
## [1] 3
```

```r
list(head=head(iris, 2), tail=tail(iris, 2))
```

```
## $head
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 
## $tail
##     Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 149          6.2         3.4          5.4         2.3 virginica
## 150          5.9         3.0          5.1         1.8 virginica
```

or matrices can have names (which are called dimnames)

```r
m<-matrix(data = 1:4, nrow = 2, ncol = 2, byrow = F)
dimnames(m)<-list(c("a", "b"), c("c", "d")) #rownames, columnnames
m
```

```
##   c d
## a 1 3
## b 2 4
```
