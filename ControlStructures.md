---
title: "Control Structures"
author: "Varun Boodram"
date: "September 24, 2014"
output: html_document
---
### If-else construction

The format for an if else loop is

if(condition){  
        do something  
}else if {  
        do another thing  
}else {  
        do another thing  
}

```r
sety<-function(x){
        if(x>3){
                10
        }else {
                0
        }
}
y<-sety(4); y
```

```
## [1] 10
```
Rather than write a function, y can be set to the entire if-else construct

```r
x<-4
y<-if(x>3){
                10
        }else {
                0
        }
y
```

```
## [1] 10
```
Multiple condtions use & and |

```r
sety2<-function(x){
        if(x>3 & x<=6){
                y<-2
        }else{
                y<-1
        }
}
y<-sety2(6); y
```

```
## [1] 2
```

```r
y<-sety2(10); y
```

```
## [1] 1
```

```r
setanothery<-function(x){
        if (x==2 | x==4 | x ==6 | x==8 |x==10 ){
                print("x is even")
        }else if (x==1 | x==3 | x ==5 | x==7 |x==9 ){
                     print("x is odd")
             }else{
                     print("enter x between 1 and 10")
                     }

}
setanothery(3)
```

```
## [1] "x is odd"
```

```r
setanothery(8)
```

```
## [1] "x is even"
```

```r
setanothery(100)
```

```
## [1] "enter x between 1 and 10"
```
Conditional element selection can also be acheived with ifelse, but readability diminishes with complexity; probably only suitable for only a couple of conditions

```r
# ifelse(test, yes, no)
x<-1:10
ifelse(test = x<5|x>8, yes =  0, no =  x)
```

```
##  [1] 0 0 0 0 5 6 7 8 0 0
```

```r
ifelse(test = x>= 4 & x<=8, yes = ifelse(test = x!=7, yes = 0, no = 7 ), no = x)
```

```
##  [1]  1  2  3  0  0  0  7  0  9 10
```

```r
# original sety function
x<-c(3, 8)
ifelse(test = x > 3, yes = 10, no = 0)
```

```
## [1]  0 10
```

```r
# setanothery
x<-c(3, 8, 100)
ifelse(test = x==2 | x==4 | x ==6 | x==8 |x==10, yes = print("x is even"), no = ifelse(test = x==1 | x==3 | x ==5 | x==7 |x==9 , yes = print("x is odd"), no = print("enter x between 1 and 10")))
```

```
## [1] "x is even"
## [1] "x is odd"
## [1] "enter x between 1 and 10"
```

```
## [1] "x is odd"                 "x is even"               
## [3] "enter x between 1 and 10"
```
### While loop

The format of a while loop is 

while(condition is true){  
        do something  
}

```r
count<-0
while(count<10){
        print(count)
        count<-count+1
}
```

```
## [1] 0
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
```
### Repeat loop

Repeat loops initiate an infinite loop; this is pointless, unless break is called at some point, which exits the loop. For loops are prefereable

```r
c<-5
repeat{
        if(c<0 | c>8){break}
        set.seed(5)
        coin<-rbinom(n = 1, size = 1, prob = 0.5)
        if(coin<0.5){
                c<-c+1
                print(c)
        }else{
                c<-c-1
                print(c)
                }
}
```

```
## [1] 6
## [1] 7
## [1] 8
## [1] 9
```
### for loop and loop functions (the apply family)
The for loop is the most common type of operator that will be used. The format is 

for (a number of times){  
        do something  
}

```r
x<-sample(1:5); x
```

```
## [1] 4 5 1 3 2
```

```r
for (i in 1:5){
        print(x[i])
}
```

```
## [1] 4
## [1] 5
## [1] 1
## [1] 3
## [1] 2
```

```r
for (i in seq_along(x)){
        print(x[i])
}
```

```
## [1] 4
## [1] 5
## [1] 1
## [1] 3
## [1] 2
```
for loops can be nested

```r
x<-1:10
dim(x)<-c(5,2)
for (i in seq_len(nrow(x))){ #sequence along the length of nrow
        for (j in seq_len(ncol(x))){
                print(x[i,j])
        }
}
```

```
## [1] 1
## [1] 6
## [1] 2
## [1] 7
## [1] 3
## [1] 8
## [1] 4
## [1] 9
## [1] 5
## [1] 10
```
Deep nesting can result in code that is difficult to understand

Loop functions execute a loop using just one line of code. 
 
*lapply* loops over a **list** and evaluates a function on each element

```r
x<-sample(1:5); x
```

```
## [1] 3 4 5 1 2
```

```r
lapply(X = x, function(x) x)
```

```
## [[1]]
## [1] 3
## 
## [[2]]
## [1] 4
## 
## [[3]]
## [1] 5
## 
## [[4]]
## [1] 1
## 
## [[5]]
## [1] 2
```
The first argument of lapply is a **list**; the second is the function that you want to apply to each element of that list. If the first argument is not a list, it is coerced to be, which is why the above output was given as a list. 

```r
class(lapply(X = x, function(x) x)) #this is an anonymous function, cf, eg: mean, sum, etc
```

```
## [1] "list"
```
lapply returns something for every value in the input list.

```r
lapply(X = 1:4, FUN = rnorm) # the rnorm function will work with defaults
```

```
## [[1]]
## [1] -0.02378
## 
## [[2]]
## [1]  0.1489 -0.8349
## 
## [[3]]
## [1] 1.2153 1.0035 0.5849
## 
## [[4]]
## [1] -0.75303 -0.05037  1.82453  1.69518
```

```r
lapply(X = 1:4, FUN = rnorm, mean = 20 ,sd = 5) # defaults are changed outside of the brackets (just delete brackets after specifying vals)
```

```
## [[1]]
## [1] 12.2
## 
## [[2]]
## [1] 10.68 19.84
## 
## [[3]]
## [1] 21.24 18.69 16.39
## 
## [[4]]
## [1] 20.42 20.63 12.57 21.70
```
sapply is a variant of lapply that simplifies the result, if it is possible

```r
sapply(X = x, function(x) x) #possible to simplify
```

```
## [1] 3 4 5 1 2
```

```r
sapply(X = 1:4, FUN = rnorm, mean = 20 ,sd = 5) #not possible to simplify
```

```
## [[1]]
## [1] 20.83
## 
## [[2]]
## [1] 29.88 18.49
## 
## [[3]]
## [1] 16.74 14.52 15.30
## 
## [[4]]
## [1] 31.51 26.19 14.97 24.71
```
*apply* evaulates a function over the margins of an array. A margin is a fancy word meaning "by row" or "by column". Recall that an array is a list of matrices

```r
x<-1:20; dim(x)<-c(2,2,5); x
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
apply operates on this list by applying a function to margin = 1 (by rows), or margin =2 (by columns)

```r
# a matrix is a two dimensional array
y<-matrix(data = 1:10, nrow = 5, ncol = 2); y
```

```
##      [,1] [,2]
## [1,]    1    6
## [2,]    2    7
## [3,]    3    8
## [4,]    4    9
## [5,]    5   10
```

```r
apply(X=y, MARGIN = 2, FUN = sum)
```

```
## [1] 15 40
```

```r
#check
c(sum(1:5), sum(6:10))
```

```
## [1] 15 40
```

```r
# the previously constructed x is a 3 dimensional array
apply(X = x, MARGIN = 1, FUN = sum)
```

```
## [1] 100 110
```

```r
#check
c(sum(1,3,5,7,9,11,13,15,17, 19), sum(2,4,6,8,10,12,14,16,18,20))
```

```
## [1] 100 110
```

```r
# mean of all matrices in the array (collapsing the third dimension)
apply(X = x, MARGIN = c(1,2), FUN = mean)
```

```
##      [,1] [,2]
## [1,]    9   11
## [2,]   10   12
```

```r
# mean of the second and third matrices in the array
apply(X = x[,,2:3], MARGIN = c(1,2), FUN = mean)
```

```
##      [,1] [,2]
## [1,]    7    9
## [2,]    8   10
```

```r
# sum of the elements of each matrix in the array
apply(X = x, MARGIN = 3, FUN = sum)
```

```
## [1] 10 26 42 58 74
```
**tapply** applies a function to a subset of a vector. The first argument is a vector (or vector-like object), and the second argument is a factor vector of the same length as the first object, that identifies to which group each element of the original object belongs. 
In the "data types notes", there is a bit on creating a factor variable using factor(). Factor levels can also be generated with gl() (**g**enerate factor **l**evels)

```r
gl(n = 5, k = 2, length = 10) # a 10- element (length =) factor vector with 5 (n =) levels, each repeated twice
```

```
##  [1] 1 1 2 2 3 3 4 4 5 5
## Levels: 1 2 3 4 5
```

```r
gl(n = 4, k = 5, length = 20)
```

```
##  [1] 1 1 1 1 1 2 2 2 2 2 3 3 3 3 3 4 4 4 4 4
## Levels: 1 2 3 4
```

```r
x<-1:20; x
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
```

```r
f<-gl(n = 5, k = 4, length = 20); f
```

```
##  [1] 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5
## Levels: 1 2 3 4 5
```

```r
tapply(X = x, INDEX = f, FUN = mean, simplify = T)
```

```
##    1    2    3    4    5 
##  2.5  6.5 10.5 14.5 18.5
```

```r
tapply(X = x, INDEX = f, FUN = summary, simplify = T)
```

```
## $`1`
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1.00    1.75    2.50    2.50    3.25    4.00 
## 
## $`2`
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    5.00    5.75    6.50    6.50    7.25    8.00 
## 
## $`3`
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    9.00    9.75   10.50   10.50   11.20   12.00 
## 
## $`4`
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    13.0    13.8    14.5    14.5    15.2    16.0 
## 
## $`5`
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    17.0    17.8    18.5    18.5    19.2    20.0
```
**mapply** is a multivariate version of lapply and sapply. It is useful for vectorization. Think about it this way: the functions used so far (mean, sum, etc), take a single argument. Suppose we want to use a function that takes two arguments. mapply can be used here

```r
list(a=rep(1,4), b=rep(2,3), c=rep(3,2), d=rep(4, 1))
```

```
## $a
## [1] 1 1 1 1
## 
## $b
## [1] 2 2 2
## 
## $c
## [1] 3 3
## 
## $d
## [1] 4
```
It's easier to use mapply with two inputs: one for the first argument of each rep, and one for the second

```r
list<-mapply(FUN = rep, 1:4, 4:1, SIMPLIFY = T)
names(list)<-c("a","b","c","d"); list
```

```
## $a
## [1] 1 1 1 1
## 
## $b
## [1] 2 2 2
## 
## $c
## [1] 3 3
## 
## $d
## [1] 4
```
**split**  
a common idiom is 

lapply(X = split(somthing), FUN = some function)  
split does what it sounds like: it splits a list or data frame by factor

```r
split(x = x, f = f)
```

```
## $`1`
## [1] 1 2 3 4
## 
## $`2`
## [1] 5 6 7 8
## 
## $`3`
## [1]  9 10 11 12
## 
## $`4`
## [1] 13 14 15 16
## 
## $`5`
## [1] 17 18 19 20
```

```r
split(x = mtcars$mpg, f = mtcars$gear)
```

```
## $`3`
##  [1] 21.4 18.7 18.1 14.3 16.4 17.3 15.2 10.4 10.4 14.7 21.5 15.5 15.2 13.3
## [15] 19.2
## 
## $`4`
##  [1] 21.0 21.0 22.8 24.4 22.8 19.2 17.8 32.4 30.4 33.9 27.3 21.4
## 
## $`5`
## [1] 26.0 30.4 15.8 19.7 15.0
```
Note that tapply does the same thing as the idiom. 

```r
sapply(X = split(x,f), FUN = sum)
```

```
##  1  2  3  4  5 
## 10 26 42 58 74
```

```r
tapply(X = x, INDEX = f, FUN = sum)
```

```
##  1  2  3  4  5 
## 10 26 42 58 74
```
The advantage of split is that it can be used to split much more complex objects. 

```r
head(airquality)
```

```
##   Ozone Solar.R Wind Temp Month Day
## 1    41     190  7.4   67     5   1
## 2    36     118  8.0   72     5   2
## 3    12     149 12.6   74     5   3
## 4    18     313 11.5   62     5   4
## 5    NA      NA 14.3   56     5   5
## 6    28      NA 14.9   66     5   6
```

```r
s<-split(x = airquality, f = airquality$Month)
sapply(X = s, function(x) colMeans(x[,c("Ozone", "Solar.R", "Wind")], na.rm = T))
```

```
##              5      6       7       8      9
## Ozone    23.62  29.44  59.115  59.962  31.45
## Solar.R 181.30 190.17 216.484 171.857 167.43
## Wind     11.62  10.27   8.942   8.794  10.18
```
split can even split a data frame on multiple levels

```r
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```
Suppose that we are interested in how miles per gallon varies with the numbers of gears, cylinders, and carburetors. Convert each of these to a factor:

```r
f1<-as.factor(mtcars$gear)
f2<-as.factor(mtcars$cyl)
f3<-as.factor(mtcars$carb)
#view interactions
interaction(f1,f2,f3)
```

```
##  [1] 4.6.4 4.6.4 4.4.1 3.6.1 3.8.2 3.6.1 3.8.4 4.4.2 4.4.2 4.6.4 4.6.4
## [12] 3.8.3 3.8.3 3.8.3 3.8.4 3.8.4 3.8.4 4.4.1 4.4.2 4.4.1 3.4.1 3.8.2
## [23] 3.8.2 3.8.4 3.8.2 4.4.1 5.4.2 5.4.2 5.8.4 5.6.6 5.8.8 4.4.2
## 54 Levels: 3.4.1 4.4.1 5.4.1 3.6.1 4.6.1 5.6.1 3.8.1 4.8.1 5.8.1 ... 5.8.8
```

```r
# obtain a ridiculously fine grain of detail
split(x = mtcars, f = list(f1,f2,f3))
```

```
## $`3.4.1`
##                mpg cyl  disp hp drat    wt  qsec vs am gear carb
## Toyota Corona 21.5   4 120.1 97  3.7 2.465 20.01  1  0    3    1
## 
## $`4.4.1`
##                 mpg cyl  disp hp drat    wt  qsec vs am gear carb
## Datsun 710     22.8   4 108.0 93 3.85 2.320 18.61  1  1    4    1
## Fiat 128       32.4   4  78.7 66 4.08 2.200 19.47  1  1    4    1
## Toyota Corolla 33.9   4  71.1 65 4.22 1.835 19.90  1  1    4    1
## Fiat X1-9      27.3   4  79.0 66 4.08 1.935 18.90  1  1    4    1
## 
## $`5.4.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.6.1`
##                 mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Hornet 4 Drive 21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Valiant        18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
## 
## $`4.6.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.6.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.8.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.8.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.1`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.4.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.4.2`
##              mpg cyl  disp  hp drat    wt  qsec vs am gear carb
## Merc 240D   24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
## Merc 230    22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
## Honda Civic 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
## Volvo 142E  21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2
## 
## $`5.4.2`
##                mpg cyl  disp  hp drat    wt qsec vs am gear carb
## Porsche 914-2 26.0   4 120.3  91 4.43 2.140 16.7  0  1    5    2
## Lotus Europa  30.4   4  95.1 113 3.77 1.513 16.9  1  1    5    2
## 
## $`3.6.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.6.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.6.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.8.2`
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Dodge Challenger  15.5   8  318 150 2.76 3.520 16.87  0  0    3    2
## AMC Javelin       15.2   8  304 150 3.15 3.435 17.30  0  0    3    2
## Pontiac Firebird  19.2   8  400 175 3.08 3.845 17.05  0  0    3    2
## 
## $`4.8.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.2`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.4.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.4.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.4.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.6.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.6.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.6.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.8.3`
##              mpg cyl  disp  hp drat   wt qsec vs am gear carb
## Merc 450SE  16.4   8 275.8 180 3.07 4.07 17.4  0  0    3    3
## Merc 450SL  17.3   8 275.8 180 3.07 3.73 17.6  0  0    3    3
## Merc 450SLC 15.2   8 275.8 180 3.07 3.78 18.0  0  0    3    3
## 
## $`4.8.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.3`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.4.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.4.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.4.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.6.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.6.4`
##                mpg cyl  disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4     21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
## Merc 280      19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
## Merc 280C     17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
## 
## $`5.6.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.8.4`
##                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
## Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
## Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
## Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
## Camaro Z28          13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
## 
## $`4.8.4`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.4`
##                 mpg cyl disp  hp drat   wt qsec vs am gear carb
## Ford Pantera L 15.8   8  351 264 4.22 3.17 14.5  0  1    5    4
## 
## $`3.4.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.4.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.4.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.6.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.6.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.6.6`
##               mpg cyl disp  hp drat   wt qsec vs am gear carb
## Ferrari Dino 19.7   6  145 175 3.62 2.77 15.5  0  1    5    6
## 
## $`3.8.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.8.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.6`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.4.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.4.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.4.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.6.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.6.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.6.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`3.8.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`4.8.8`
##  [1] mpg  cyl  disp hp   drat wt   qsec vs   am   gear carb
## <0 rows> (or 0-length row.names)
## 
## $`5.8.8`
##               mpg cyl disp  hp drat   wt qsec vs am gear carb
## Maserati Bora  15   8  301 335 3.54 3.57 14.6  0  1    5    8
```
