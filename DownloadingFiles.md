---
title: 'CGD: Downloading Files'
date: "September 24, 2014"
output: html_document
---
## Downloading the files
First steps

* know what directory you are in 
* use getwd() and setwd(), as necessary
* setwd("../") moves up one directory

1. Create a directory for the data to fall into, if such a directory doesn't already exist

if (!file.exists("directory name")){  
        dir.create("directory name")  
}

2. Get the data. download.file() is the main way that we obtain internet data
. *Always* include the time of download

download.file(url = , destfile = "./directory name/filename.ext", method = "curl")  
dateDownloaded<-date()

The downloaded files can be viewed with list.files("./directory name")

3. The file is now stored locally. The most commonly used function to read it into R is read.table(). It is the most robust, and allows the most flexibility.   
However, because of its slowness, it is not the best method to read large tables into R. 

eg: downloading data on [Baltimore Fixed Speed Camera](https://data.baltimorecity.gov/Transportation/Baltimore-Fixed-Speed-Cameras/dz54-2aru) data and reading into R

```r
# Having checked that we are in the correct working directory

# The following function downloads the .csv file containing data for Balitmore's Fixed Speed Cameras

#Input: Link to the file, desired directory name, desired, file name, and file extension
#Output: The path to the required file

getandload<-function(fileUrl, dir, filename, ext){
        # Step 1: create directory, if it is not already present
        dirName<-paste("./", dir, sep = "")
        if(!file.exists(dirName)){
                dir.create(path = dirName)
        }
        # Step 2: Get the data, unless this step has already been done
        dest<-paste(dirName,"/", filename, ext, sep = "")
        if(!file.exists(dest)){
                download.file(url = fileUrl, destfile = dest, method = "curl") 
                datedownloaded<-date()
        }
        dest
}
fileUrl<-"https://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
dest<-getandload(fileUrl, dir="camera", filename="camera", ext = ".csv")
# Step 3: load the data
# because the variable names are included at the top of each file, header = T
camdat<-read.table(file = dest, header = T, sep = ",")
```
In the case that we are working with a .csv file, as here, read.csv() can be used. It sets sep =", " and header =T by default

```r
camdat2<-read.csv("./camera/camera.csv")
all.equal(camdat, camdat2)
```

```
## [1] TRUE
```

```r
rm(camdat2)
```
_*Troubleshooting*_ 

* set quote ="" to tell R to ignore quoted values
* na.strings = sets the character that represents the missing values

## Reading in particular file formats

### Excel 

1. require the xlsx package
2. run getandload() with the appropriate parameters
3. in step 3, use read.xlsx() instead of read.table() specifying which sheet the data is stored on with sheetIndex, and specify the header, if necessary


```r
fileUrl<-"https://data.baltimorecity.gov/api/views/dz54-2aru/rows.xlsx?accessType=DOWNLOAD"
dest<-getandload(fileUrl = fileUrl, dir = "camera", filename = "camera", ext = ".xls") #This is not supposed to happen. The ext should be ".xlsx", but it only works this way
require("xlsx")
camdat2<-read.xlsx(file = dest, sheetIndex = 1, header = T)
```
Nice things

* you can read specific rows and columns

```r
colInd<-2:3; rowInd<-1:4
camdat2subset<-read.xlsx(file = dest, sheetIndex = 1, rowIndex = rowInd, colIndex = colInd, header = T)
camdat2subset
```

```
##   direction      street
## 1       N/B   Caton Ave
## 2       S/B   Caton Ave
## 3       E/B Wilkens Ave
```
* you can write an .xlsx file out with 

```r
write.xlsx(camdat2subset, file = "./camera/camera2.xlsx")
```

### XML

* short for "extensible markup language"
* frequently used to store structured data
* widely used in internet applications
* extracting XML is the basis for most web scraping

**Tags** correspond to general labels

* start tags < section >
* end tags < \section >

**Elements** are specific examples of tags

eg: < Greeting > Hello <\\ greeting >

Unlike the previous examples, we do **not** use download file. Instead use xmlTreeParse to parse out the XML file given in the URL

```r
require(XML)
fileUrl<-"http://www.w3schools.com/xml/simple.xml"
doc<-xmlTreeParse(file = fileUrl, useInternalNodes = T)
doc
```

```
## <?xml version="1.0" encoding="UTF-8"?>
## <!-- Edited by XMLSpy -->
## <breakfast_menu>
##   <food>
##     <name>Belgian Waffles</name>
##     <price>$5.95</price>
##     <description>Two of our famous Belgian Waffles with plenty of real maple syrup</description>
##     <calories>650</calories>
##   </food>
##   <food>
##     <name>Strawberry Belgian Waffles</name>
##     <price>$7.95</price>
##     <description>Light Belgian waffles covered with strawberries and whipped cream</description>
##     <calories>900</calories>
##   </food>
##   <food>
##     <name>Berry-Berry Belgian Waffles</name>
##     <price>$8.95</price>
##     <description>Light Belgian waffles covered with an assortment of fresh berries and whipped cream</description>
##     <calories>900</calories>
##   </food>
##   <food>
##     <name>French Toast</name>
##     <price>$4.50</price>
##     <description>Thick slices made from our homemade sourdough bread</description>
##     <calories>600</calories>
##   </food>
##   <food>
##     <name>Homestyle Breakfast</name>
##     <price>$6.95</price>
##     <description>Two eggs, bacon or sausage, toast, and our ever-popular hash browns</description>
##     <calories>950</calories>
##   </food>
## </breakfast_menu>
## 
```
doc is parsed into nodes. The topmost node wraps the entire document. In this case it's < breakfast_menu>

```r
top<-xmlRoot(doc)
xmlName(top)
```

```
## [1] "breakfast_menu"
```
The elements in between the root node is given by names. Each item is wrapped within a "food" element

```r
#child nodes of this root
names(top)
```

```
##   food   food   food   food   food 
## "food" "food" "food" "food" "food"
```
Accessing elements in an XML file is totally analogous to accessing elements of a list. 

```r
#first element of the rootnode
top[[1]]
```

```
## <food>
##   <name>Belgian Waffles</name>
##   <price>$5.95</price>
##   <description>Two of our famous Belgian Waffles with plenty of real maple syrup</description>
##   <calories>650</calories>
## </food>
```

```r
names(top[[1]])
```

```
##          name         price   description      calories 
##        "name"       "price" "description"    "calories"
```

```r
#Suppose we want to extract the price of the first element
top[[1]][["price"]]
```

```
## <price>$5.95</price>
```

```r
# Extract the price  variable of all elements
xpathSApply(doc = top, "//price", xmlValue)
```

```
## [1] "$5.95" "$7.95" "$8.95" "$4.50" "$6.95"
```

```r
# similarly
xpathSApply(doc=top, "//name", xmlValue)
```

```
## [1] "Belgian Waffles"             "Strawberry Belgian Waffles" 
## [3] "Berry-Berry Belgian Waffles" "French Toast"               
## [5] "Homestyle Breakfast"
```

### HTML

Right click [here](view-source:http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens), and view the source. We want to drill into this source code and extract some information

Load the data with **html**TreeParse. Remember to delete "view source" from the head of the url

```r
fileUrl<-"http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens"
doc<-htmlTreeParse(file = fileUrl, useInternalNodes = T)
str(doc)
```

```
## Classes 'HTMLInternalDocument', 'HTMLInternalDocument', 'XMLInternalDocument', 'XMLAbstractDocument' <externalptr>
```
Look for "list items" (li) with a particular class (in the example below, equal to score)

```r
xpathSApply(doc, "//li[@class ='score']", xmlValue)
```

```
## [1] "23-16" "26-6"  "23-21" "38-10"
```

```r
xpathSApply(doc, "//li[@class ='team-name']", xmlValue)
```

```
##  [1] "Cincinnati"   "Pittsburgh"   "Cleveland"    "Carolina"    
##  [5] "Indianapolis" "Tampa Bay"    "Atlanta"      "Cincinnati"  
##  [9] "Pittsburgh"   "Tennessee"    "New Orleans"  "San Diego"   
## [13] "Miami"        "Jacksonville" "Houston"      "Cleveland"
```
### JSON
JSON files are similar to XML files insofar as they are structured, and is very commonly used in Application Programming Interfaces. APIs are how you can access the data for companies like Twitter or facebook through URLs.   
Click [here](https://api.github.com/users/jtleek/repos) to obtain the API for the github API containing data about the repos that the instructor's contributing to.   
Reading data from a JSON file is similar to reading from an XML file

```r
require(jsonlite)
fileUrl<-"https://api.github.com/users/jtleek/repos"
jsonData<-fromJSON(fileUrl)
names(jsonData)
```

```
##  [1] "id"                "name"              "full_name"        
##  [4] "owner"             "private"           "html_url"         
##  [7] "description"       "fork"              "url"              
## [10] "forks_url"         "keys_url"          "collaborators_url"
## [13] "teams_url"         "hooks_url"         "issue_events_url" 
## [16] "events_url"        "assignees_url"     "branches_url"     
## [19] "tags_url"          "blobs_url"         "git_tags_url"     
## [22] "git_refs_url"      "trees_url"         "statuses_url"     
## [25] "languages_url"     "stargazers_url"    "contributors_url" 
## [28] "subscribers_url"   "subscription_url"  "commits_url"      
## [31] "git_commits_url"   "comments_url"      "issue_comment_url"
## [34] "contents_url"      "compare_url"       "merges_url"       
## [37] "archive_url"       "downloads_url"     "issues_url"       
## [40] "pulls_url"         "milestones_url"    "notifications_url"
## [43] "labels_url"        "releases_url"      "created_at"       
## [46] "updated_at"        "pushed_at"         "git_url"          
## [49] "ssh_url"           "clone_url"         "svn_url"          
## [52] "homepage"          "size"              "stargazers_count" 
## [55] "watchers_count"    "language"          "has_issues"       
## [58] "has_downloads"     "has_wiki"          "has_pages"        
## [61] "forks_count"       "mirror_url"        "open_issues_count"
## [64] "forks"             "open_issues"       "watchers"         
## [67] "default_branch"
```
jsonData is a *data frame* of *data frames*. 

```r
class(jsonData)
```

```
## [1] "data.frame"
```

```r
# recall how to subset a data frame
c(head(jsonData[1]), head(jsonData["id"]))
```

```
## $id
## [1] 12441219 20234724  7751816  4772877 14204342 23840078
## 
## $id
## [1] 12441219 20234724  7751816  4772877 14204342 23840078
```

```r
jsonData$id
```

```
##  [1] 12441219 20234724  7751816  4772877 14204342 23840078 11549405
##  [8] 14240696 11976743  8730097 14590772 12563551  6582536  6661008
## [15] 19133476 16584923  7745123 15639612 19133794 17446438 15723485
## [22] 11378145 17711648 20548045 16103392 23202748 12134722 13788992
## [29] 15532926 12931390
```

```r
# jsonData$owner is another dataframe
names(jsonData$owner)
```

```
##  [1] "login"               "id"                  "avatar_url"         
##  [4] "gravatar_id"         "url"                 "html_url"           
##  [7] "followers_url"       "following_url"       "gists_url"          
## [10] "starred_url"         "subscriptions_url"   "organizations_url"  
## [13] "repos_url"           "events_url"          "received_events_url"
## [16] "type"                "site_admin"
```

```r
#so we can drill further down, eg
jsonData$owner$login
```

```
##  [1] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
##  [8] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
## [15] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
## [22] "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek" "jtleek"
## [29] "jtleek" "jtleek"
```
If we have to export to an API that requires API formatted data, use toJSON. To view the file use the **C**oncatenate **A**nd Prin**t** command, cat()

```r
myJson<-toJSON(iris, pretty = T)
head(cat(myJson))
```

```
## [
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.7,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.6,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.6,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3.9,
## 		"Petal.Length" : 1.7,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.6,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.4,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.1,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3.7,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.8,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.8,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.1,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.3,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.1,
## 		"Petal.Width" : 0.1,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 4,
## 		"Petal.Length" : 1.2,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 4.4,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3.9,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 1.7,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.7,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.7,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.6,
## 		"Sepal.Width" : 3.6,
## 		"Petal.Length" : 1,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 1.7,
## 		"Petal.Width" : 0.5,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.8,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.9,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.2,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.2,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.7,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.8,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.2,
## 		"Sepal.Width" : 4.1,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.1,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 4.2,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 1.2,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 3.6,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.1,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.4,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.5,
## 		"Sepal.Width" : 2.3,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.4,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 1.3,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.5,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.6,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 1.9,
## 		"Petal.Width" : 0.4,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.8,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.3,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 1.6,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 4.6,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5.3,
## 		"Sepal.Width" : 3.7,
## 		"Petal.Length" : 1.5,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 1.4,
## 		"Petal.Width" : 0.2,
## 		"Species" : "setosa"
## 	},
## 	{
## 		"Sepal.Length" : 7,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 4.7,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.9,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 4.9,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 2.3,
## 		"Petal.Length" : 4,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.5,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.6,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 4.7,
## 		"Petal.Width" : 1.6,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 2.4,
## 		"Petal.Length" : 3.3,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.6,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.6,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.2,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 3.9,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 2,
## 		"Petal.Length" : 3.5,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.9,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.2,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 2.2,
## 		"Petal.Length" : 4,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.7,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 3.6,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 4.4,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 4.1,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.2,
## 		"Sepal.Width" : 2.2,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 3.9,
## 		"Petal.Width" : 1.1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.9,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 4.8,
## 		"Petal.Width" : 1.8,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 4.9,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.7,
## 		"Petal.Width" : 1.2,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.3,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.6,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.4,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.8,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.8,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5,
## 		"Petal.Width" : 1.7,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 2.6,
## 		"Petal.Length" : 3.5,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 2.4,
## 		"Petal.Length" : 3.8,
## 		"Petal.Width" : 1.1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 2.4,
## 		"Petal.Length" : 3.7,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 3.9,
## 		"Petal.Width" : 1.2,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 1.6,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.4,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.6,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 4.7,
## 		"Petal.Width" : 1.5,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.3,
## 		"Petal.Length" : 4.4,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.1,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 4,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.5,
## 		"Sepal.Width" : 2.6,
## 		"Petal.Length" : 4.4,
## 		"Petal.Width" : 1.2,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.6,
## 		"Petal.Width" : 1.4,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.6,
## 		"Petal.Length" : 4,
## 		"Petal.Width" : 1.2,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5,
## 		"Sepal.Width" : 2.3,
## 		"Petal.Length" : 3.3,
## 		"Petal.Width" : 1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 4.2,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.2,
## 		"Petal.Width" : 1.2,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.2,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.2,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 4.3,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.1,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 3,
## 		"Petal.Width" : 1.1,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.1,
## 		"Petal.Width" : 1.3,
## 		"Species" : "versicolor"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 6,
## 		"Petal.Width" : 2.5,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 1.9,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.1,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.9,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.5,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.8,
## 		"Petal.Width" : 2.2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.6,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 6.6,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 4.9,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 4.5,
## 		"Petal.Width" : 1.7,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.3,
## 		"Sepal.Width" : 2.9,
## 		"Petal.Length" : 6.3,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 5.8,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.2,
## 		"Sepal.Width" : 3.6,
## 		"Petal.Length" : 6.1,
## 		"Petal.Width" : 2.5,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.5,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 5.3,
## 		"Petal.Width" : 1.9,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.8,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.5,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.7,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 5,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 2.4,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 5.3,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.5,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.5,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.7,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 6.7,
## 		"Petal.Width" : 2.2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.7,
## 		"Sepal.Width" : 2.6,
## 		"Petal.Length" : 6.9,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 2.2,
## 		"Petal.Length" : 5,
## 		"Petal.Width" : 1.5,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.9,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 5.7,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.6,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.9,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.7,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 6.7,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 4.9,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 5.7,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.2,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 6,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.2,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 4.8,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.9,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.2,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.8,
## 		"Petal.Width" : 1.6,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.4,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 6.1,
## 		"Petal.Width" : 1.9,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.9,
## 		"Sepal.Width" : 3.8,
## 		"Petal.Length" : 6.4,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 2.2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.8,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 1.5,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.1,
## 		"Sepal.Width" : 2.6,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 1.4,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 7.7,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 6.1,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 2.4,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.4,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 5.5,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 4.8,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.9,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 5.4,
## 		"Petal.Width" : 2.1,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 5.6,
## 		"Petal.Width" : 2.4,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.9,
## 		"Sepal.Width" : 3.1,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.8,
## 		"Sepal.Width" : 2.7,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 1.9,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.8,
## 		"Sepal.Width" : 3.2,
## 		"Petal.Length" : 5.9,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3.3,
## 		"Petal.Length" : 5.7,
## 		"Petal.Width" : 2.5,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.7,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.2,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.3,
## 		"Sepal.Width" : 2.5,
## 		"Petal.Length" : 5,
## 		"Petal.Width" : 1.9,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.5,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.2,
## 		"Petal.Width" : 2,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 6.2,
## 		"Sepal.Width" : 3.4,
## 		"Petal.Length" : 5.4,
## 		"Petal.Width" : 2.3,
## 		"Species" : "virginica"
## 	},
## 	{
## 		"Sepal.Length" : 5.9,
## 		"Sepal.Width" : 3,
## 		"Petal.Length" : 5.1,
## 		"Petal.Width" : 1.8,
## 		"Species" : "virginica"
## 	}
## ]
```

```
## NULL
```
