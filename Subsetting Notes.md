---
title: "Subsetting"
author: "Varun Boodram"
date: "September 23, 2014"
output: html_document
---

Depending on the class of the R object, different operators will have to be used to extract subsets of the object  

### Numerical subsetting

* [ ] always returns an object of the **same** class as the original. 

eg: vectors

```r
x<--(1:10); x
```

```
##  [1]  -1  -2  -3  -4  -5  -6  -7  -8  -9 -10
```

```r
x[3] # third element of the vector
```

```
## [1] -3
```

```r
c(class(x), class(x[3]))
```

```
## [1] "integer" "integer"
```
eg: lists

```r
l<-list(foo=1:4, bar=matrix(data = 1:4,nrow = 2, ncol = 2, byrow = T))
l
```

```
## $foo
## [1] 1 2 3 4
## 
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
l[2] # second element of the list
```

```
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
c(class(l), class(l[2]))
```

```
## [1] "list" "list"
```
Note that l[2] is a list of one element, called "bar", which is a matrix. If the elements of a list are named (as above), the name of an element index can be used to return a list. This is preferrable to use, as you don't have to remember where the element is in a list

```r
l["bar"]
```

```
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
#for fun, change the names of the list
names(l)<-c("hello", "goodbye")
l["goodbye"]
```

```
## $goodbye
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```
Extracting multiple elements from a list *requires* the use of [ ]. 

```r
l2<-list(foo=1:4, bar=matrix(data = 1:4,nrow = 2, ncol = 2, byrow = T), norf = "hello")
l2[c(1,3)]
```

```
## $foo
## [1] 1 2 3 4
## 
## $norf
## [1] "hello"
```

```r
l2[c("bar","norf")]
```

```
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
## 
## $norf
## [1] "hello"
```
eg: data frame

```r
head(iris[3])
```

```
##   Petal.Length
## 1          1.4
## 2          1.4
## 3          1.3
## 4          1.5
## 5          1.4
## 6          1.7
```

```r
c(class(iris), class(iris[3]))
```

```
## [1] "data.frame" "data.frame"
```
Matrices are an exception to the rule, "[ ] always returns an object of the same class as the orginal object." Instead of a *matrix*, by default, subsetting results in a *vector*   
eg: matrix

```r
m<-1:10
dim(m)<-c(2, 5)
m[1,4] # row 1, column 4
```

```
## [1] 7
```

```r
c(class(m), class(m[1,4]))
```

```
## [1] "matrix"  "integer"
```
This behaviour can be turned off by setting the drop argument to F

```r
m[1,4, drop =F]
```

```
##      [,1]
## [1,]    7
```

```r
c(class(m), class(m[1,4, drop =F]))
```

```
## [1] "matrix" "matrix"
```
* [[ ]] is used to extract *a single element* of a **list** or a **data frame**. This element may or may not be of the same class as the subsetted object

eg: lists

```r
names(l)<-c("foo", "bar")
l[[2]]
```

```
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
c(class(l), class(l[[2]]))
```

```
## [1] "list"   "matrix"
```
The difference between l[2] and l[[2]] is that l[2] returns the one-element *list*, l[[2]] returns the object(s) in that list.   
If the numerical index has a name, then the name can be used instead of the numerical index

```r
l[["foo"]]
```

```
## [1] 1 2 3 4
```

```r
l[["bar"]]
```

```
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```
Suppose you want to access the third element of the vector named foo. This is accomplished with *pairs of [[ ]]*

```r
l[[1]][[3]]
```

```
## [1] 3
```
An alternative syntax uses a vector to access the element

```r
c(l[[c(1,3)]], l[[c(2, 3)]])
```

```
## [1] 3 2
```
eg: data frame 

```r
head(iris[[1]])
```

```
## [1] 5.1 4.9 4.7 4.6 5.0 5.4
```

```r
c(class(iris), class(iris[[1]]))
```

```
## [1] "data.frame" "numeric"
```
* $ is used to extract elements of a **list** or a **data frame** by name. The extracted element may or may not be of the same class as the original object. 
eg: list

```r
l$foo
```

```
## [1] 1 2 3 4
```

```r
l$bar
```

```
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
c(class(l), class(l$foo), class(l$bar))
```

```
## [1] "list"    "integer" "matrix"
```
$ can *only* be used with literal names. 

```r
myname<-"foo"
l$myname
```

```
## NULL
```
Because myname is *not* a literal name, l$myname is meaningless. To obtain the object(s) stored under the name myname, [[ ]] are required

```r
l[[myname]]
```

```
## [1] 1 2 3 4
```
Thus,  *computed* indices are accessed with [[ ]]   

### Logical subsetting
This is used when you want to subset by a *condition*. 
eg: vector. The condition goes inside the brackets

```r
x<-c("a","b", "c", "c", "d", "a")
x[x>"a"]
```

```
## [1] "b" "c" "c" "d"
```
Logical indexing can be used to create a logical vector

```r
u<-x>"a"; u
```

```
## [1] FALSE  TRUE  TRUE  TRUE  TRUE FALSE
```
This logical vector can now be used as a condition

```r
x[u]
```

```
## [1] "b" "c" "c" "d"
```
This princliple can be used to remove missing values. 

```r
x<-c(1:8, rep(NA, 8))
x<-sample(x); x
```

```
##  [1]  8 NA  4 NA NA NA NA NA NA NA  3  5  7  2  6  1
```

```r
keep<-!is.na(x); keep
```

```
##  [1]  TRUE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
## [12]  TRUE  TRUE  TRUE  TRUE  TRUE
```

```r
x[keep]
```

```
## [1] 8 4 3 5 7 2 6 1
```
The complete.cases() function can be invoked when you want to perform a similar operation on more than one vector, *each of which have the same length*

```r
x<-c(1:8, rep(NA, 8)); x<-sample(x); x
```

```
##  [1]  7 NA  2  8 NA NA  5 NA NA  3 NA  6 NA  1 NA  4
```

```r
y<-c(letters[1:8], rep(NA, 8)); y<-sample(y); y
```

```
##  [1] NA  "a" "f" "g" NA  NA  NA  NA  "h" "b" "e" "d" NA  NA  NA  "c"
```

```r
keep<-complete.cases(x,y)
rbind(x[keep], y[keep])
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,] "2"  "8"  "3"  "6"  "4" 
## [2,] "f"  "g"  "b"  "d"  "c"
```

Logical subsetting works on the same principles as numerical subsetting

```r
l
```

```
## $foo
## [1] 1 2 3 4
## 
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
l[names(l)=="bar"]
```

```
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
```

```r
l[names(l)>"bar"]
```

```
## $foo
## [1] 1 2 3 4
```

```r
l2
```

```
## $foo
## [1] 1 2 3 4
## 
## $bar
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
## 
## $norf
## [1] "hello"
```

```r
l2[names(l2)>"bar" | names(l2)=="norf"]
```

```
## $foo
## [1] 1 2 3 4
## 
## $norf
## [1] "hello"
```
