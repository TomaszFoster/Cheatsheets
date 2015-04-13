
### Week 1

##### Download a file
``` r
fileUrl <- "https://api.example.com/table.csv?access=DOWNLOAD"
download.file( fileUrl , destfile = "./data/table.csv", method="curl")
list.files("./data")

# create directory
dir.create('dir')

# check if directory or file exists - boolean
file.exists('file.csv')

# read data
# these are the important variables for read.table()
read.table('file', header = FALSE, sep = "", row.names, nrows = -1)

# options
# na.strings 	- set character respresenting missing value
# nrows 		- how many rows of the file to read
# skip 			- number of lines to skip before reading
# quote 		- where there are any quoted values ( setting quote="" solves a lot of problems usually )

read.csv(file, header = TRUE, sep = ",", quote = "\"", dec = ".", fill = TRUE, comment.char = "", ...)

read.csv2(file, header = TRUE, sep = ";", quote = "\"", dec = ",", fill = TRUE, comment.char = "", ...)

read.delim(file, header = TRUE, sep = "\t", quote = "\"", dec = ".", fill = TRUE, comment.char = "", ...)

read.delim2(file, header = TRUE, sep = "\t", quote = "\"", dec = ",", fill = TRUE, comment.char = "", ...)

# get info and first few lines of file or variable
head('file')
```
##### Parsing Excel files
``` r
library(xlsx)
read.xlsx( 'file.xlsx', sheetIndex=1, header=TRUE )
# optional params colIndex=colIndex, rowIndex=rowIndex for getting subset

# to write to xlsx
write.xlsx()
# read.xlsx2() is faster but not suited for subsets
# store in .csv or .tab files or database for distribution
```
##### Parsing XML & HTML
``` r
library(XML)
doc <- xmlTreeParse(fileUrl, useInternal=TRUE)	# parse an XML document
doc <- htmlTreePars(firlUrl, useInternal=TRUE) 	# same as above but for HTML file
rootNode <- xmlRoot( doc ) 						# gets wrapper element
xmlName(rootNode) 								# gets name of node

# to access items
rootNode[[1]]									# gets first element or array
rootNode[[1]][[1]]								# gets first element or array, then first sub element or array

xpathSApply( rootNode, xmlValue )				# programatically extract parts of a file

# XPath
/node 											# top level node
//node 											# node at any level
node[@attr-name] 								# node with attribute name
node[@attr-name='bob'] 							# node with attribute name attr-name = "bob"

xpathSApply( rootNode, "//name", xmlValue )		# gets value of all attributes with name of "name"
xpathSApply( rootNode, "//price", xmlValue )
xpathSApply( doc, "//li[@class='team-name']", xmlValue) # use this format to parse HTML
```
##### Parsing JSON data
``` r
library(jsonlite)
jsonData <- fromJSON("http://api.github.com/users/tomaszfoster/repos")
names(jsonData)

###### output
 [1] "id"                "name"              "full_name"         "owner"             "private"          
 [6] "html_url"          "description"       "fork"              "url"               "forks_url"        
[11] "keys_url"          "collaborators_url" "teams_url"         "hooks_url"         "issue_events_url" 
[16] "events_url"        "assignees_url"     "branches_url"      "tags_url"          "blobs_url"        
[21] "git_tags_url"      "git_refs_url"      "trees_url"         "statuses_url"      "languages_url"    
[26] "stargazers_url"    "contributors_url"  "subscribers_url"   "subscription_url"  "commits_url"      
[31] "git_commits_url"   "comments_url"      "issue_comment_url" "contents_url"      "compare_url"      
[36] "merges_url"        "archive_url"       "downloads_url"     "issues_url"        "pulls_url"        
[41] "milestones_url"    "notifications_url" "labels_url"        "releases_url"      "created_at"       
[46] "updated_at"        "pushed_at"         "git_url"           "ssh_url"           "clone_url"        
[51] "svn_url"           "homepage"          "size"              "stargazers_count"  "watchers_count"   
[56] "language"          "has_issues"        "has_downloads"     "has_wiki"          "has_pages"        
[61] "forks_count"       "mirror_url"        "open_issues_count" "forks"             "open_issues"      
[66] "watchers"          "default_branch"   
###### end output

myjson <- toJSON(iris, pretty=TRUE)		# pretty=TRUE shows nicely formatted JSON
cat(myjson)

iris2 <- fromJSON(myjson) 				# send the data back to a data.frame from a json file
```
##### Data tables
``` r
library(data.table)
# create data tables just like data frames
# example data frame
DF = data.frame( x = rnorm(9), y = rep( c("a","b","c"), each = 3), z = rnorm(9) )
# example data table
DT = data.table( x = rnorm(9), y = rep( c("a","b","c"), each = 3), z = rnorm(9) )

tables() 	# tells you name, num rows, num cols, how many MB, if there is a key

# For subsetting, when you only pass one variable, unlike data frames, it will use that value for number of rows
DT[ c( 2, 3 ) ] 	# this will give me the second and third rows

# for subsetting columns it is modified for data tables
DT[ , c( 2, 3 ) ]
DT[ , someFunction(x) ]				# you can pass functions!
DT[ , list( sum(x), mean(y) ) ]		# you can return sum of x values and mean of y values
DT[ , w:=z^2 ]						# you can add columns FAST. here, new w columns = square of z values
```
Be careful when copying data tables. When you copy a data FRAME `DF2 <- DF` it makes a copy whereas
when you copy a data TABLE `DT2 <- DT` it's not a copy, it's a pointer. Changes made to `DT` will show up in `DT2`!

You can also do multiple operations with Data Tables
``` r
DT[, m:={ tmp <- (x+z) ; log2( tmp + 5 ) }]
```
which returns the `log2( x + z + 5 )` since in functions in R, we return the value of the last expression

Special variable `.N` is a faster in `DT[, .N, by=x ]` than equivalent function `DT$x`

Subsetting and sorting data tables is much faster than in data frames

##### Joins
You can use keys to facilitate joining two data tables
``` r
DT1 <- data.table( x=c( "a", "a", "b", "dt1"), y=1:4 )
DT2 <- data.table( x=c( "a", "b", dt2"), z=5:7 )

setkey(DT1, x); setkey(DT2, x)
merge(DT1, DT2)

# returns this data table
   x  y  z
1: a  1  5
2: a  2  5
3: b  3  6
```
Reading a data.table is much faster than reading a data.frame. It's faster and more efficient but requires
more care in use, however you can do more with data.frames

### Week 2

##### Connecting to MySQL
```r
# install RMySQL package
install.packages("RMySQL")

# connect to a database - example is ucsc database
ucscDb <- dbConnect(MySQL(),user="genome", host="genome-mysql.cse.ucsc.edu")

result <- dbGetQuery(ucscDb,"show databases;"); 

# you need to remember to disconnect from the database
dbDisconnect(ucscDb);

# printing result here will show a list of all databases
result

# connect to hg19 database and list tables
hg19 <- dbConnect(MySQL(),user="genome", db="hg19", host="genome-mysql.cse.ucsc.edu")

# dbListTables is pretty self explanatory
allTables <- dbListTables(hg19)

length(allTables)

allTables[1:5]

# get dimensions of a specific table
dbListFields(hg19,"affyU133Plus2")

dbGetQuery(hg19, "select count(*) from affyU133Plus2")

# Read from the table
affyData <- dbReadTable(hg19, "affyU133Plus2")

head(affyData)

# select a specific subset
query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3")

affyMis <- fetch(query); quantile(affyMis$misMatches)

affyMisSmall <- fetch(query,n=10); dbClearResult(query);

dim(affyMisSmall)

# don't forget to close the connetion !

dbDisconnect(hg19)
```

##### Reading HDF5
+ Used for large data sets
+ Supports storing a range of data types
+ Heirarchical data format
+ `groups` - containing zero or more data sets and metadata
 + have a group header with group name and list of attributes
 + have a group symbol table with a list of objects in group
+ `datasets` - multidimensional array of data elements with metadata
 + have a header with name, datatype, dataspace, and storage layout
 + have a data array with the data

###### R HDF5 Package
```r
source("http://bioconductor.org/biocLite.R")
biocLite("rhdf5")

# this installs packages from Bioconductor which is primarily used for genomics but also has
# good Big Data packages
library(rhdf5)
created = h5createFile("example.h5")
created
```
##### Create groups
```r
created = h5createGroup("example.h5","foo")
created = h5createGroup("example.h5","baa")
created = h5createGroup("example.h5","foo/foobaa")
h5ls("example.h5")
```
##### Write to groups
```r
A = matrix(1:10, nr=5, nc=2)
h5write(A, "example.h5", "foo/A")
B = array(seq(0.1,2.0, by=0.1), dim=c(5,2,2))
attr(B, "scale") <- "liter"
h5write(B, "example.h5","foo/foobaa/B")
h5ls("example.h5)")
```

##### Write a data set
```r
df = data.frame(1L:5L,seq(0,1,length.out=5),c("ab","cde","fghi","a","s"), stringsAsFactors=FALSE)
h5write(df,"example.h5","df")
h5ls("example.h5")
```

##### Reading data
```r
readA = h5read("exmaple.h5","foo/A")
readB = h5read("exmaple.h5","foo/foobaa/B")
readdf = h5read("exmaple.h5","df")
readA
```
##### Writing and reading chunks
```r
h5write(c(12,13,14),"example.h5","foo/A",index=list(1:3,1))
h5read("example.h5","foo/A")
```
#### Reading data from the Web
##### Webscraping
+ can be a great way to get data
+ many websites have information you may want to programatically read
+ in some cases this is against their TOS
+ attempting to read too many pages too quickly can get your IP blocked (lol)

##### Getting data off webpages - readLines()
```r
con = url("http://scholar.google.com/citations?user=HI-I6C0AAAAJ&hl=en")
htmlCode = readLines(con)
close(con)
htmlCode

##### Reading data from APIs
Create an application, say on twitter
###### Accessing Twitter from R
```r
myapp = oauth_app("twitter",
	key="yourConsumerKeyHere",
	secret="youConsumerSecret")

sig = sign_oath1.0(myapp,
	token = "yourTokenHere",
	token_secret = "youTokenSecretHere")

homeTL = GET("https://api.twitter.com/1.1/statuses/home_timeline.json", sig)
```
##### Converting JSON objects
```r
json1 = content(homeTL)
json2 = jsonlite::fromJSON(toJSON(json1))
json2[1,1:4]
```
###### Just read the documentation dummy!
+ httr allows `GET`,`POST`,`PUT`,`DELETE` requests if you're authorized
+ you can authenticate with a user name or password
+ most modern APIs use something like oauth
+ httr works well with Facebook, Google, Twitter, Github, etc

##### Reading from other sources
Theres probably a package for it.
+ Easy way to find if an R package exists is to google `data storage mechanism R package`
+ for example `MySQL R Package`

##### Interacting more directly with files
```r
file 			# open a connection to a text file
url 			# open a connection to a url
gzfile			# open a connection to a .gz file
bzfile			# open connections to .bz2 file
?connections 	# for more information

## REMEMBER TO CLOSE CONNECTIONS!! ##
```
##### foreign package
+ loads data from Minitab, S, SAS, SPSS, Stata, Systat
+ Basic functions `read.foo`
```r
read.arff 		# Weka
read.dta 		# Stata
read.mtp 		# Minitab
read.octave 	# Octave
read.spss 		# SPSS
read.xport 		# SAS
```

##### Examples of other db packages
+ RPostresSQL provides DBI-compliant database connection from R
+ RODBC provides interfaces to multiple databases including PostgreQL, MySQL, Microsoft Access and SQLite.
+ RMongo and rmongodb provide access to, you guessed it, MongoDB

##### Reading images
+ [jpeg](http://cran.r-project.org/web/packages/jpeg/index.html)
+ [readbitmap](http://cran.r-project.org/web/packages/readbitmap/index.html)
+ [png](http://cran.r-project.org/web/packages/png/index.html)
+ [EBImage (Bioconductor)](http://www.bioconductor.org/packages/2.13/bioc/html/EBImage.html)

##### Reading GIS data
+ [rdgal](http://cran.r-project.org/web/packages/rgdal/index.html)
+ [rgeos](http://cran.r-project.org/web/packages/rgeos/index.html)
+ [raster](http://cran.r-project.org/web/packages/raster/index.html)

##### Reading Music Data
+ [tuneR](http://cran.r-project.org/web/packages/tuneR/)
+ [seewave](http://rug.mnhn.fr/seewave/)

##### Cool Hack for Fixed Width Files
```r
# paste a ruler into console
cat(">",paste0(rep(c(1:9,"+"),6),collapse=""))

# or just open in sublime text ;)
```

### Week 3

##### Subsetting and Sorting
###### Quick Review
```r
set.seed(13435) # sets the random seed generator, used to be able to replicate data
X <- data.fram("varl1"=sample(1:5), "var2"=sample(6:10), "var3"=sample(11:15))
X <- X[sample(1:5),]
X$var2[c(1,3)] = NA
```
##### Logicals ands and ors
```r
X[ (X$var1 <= 3 & X$var3 > 11), ]

X[(X$var1 <= 3 | X$var3 > 15), ]

# dealing with missing values
X[which(X$var2 > 8), ]
```
##### Sorting
```r
sort(X$var1)

sort(X$var1, decreasing=TRUE)

sort(X$var2, na.last=TRUE)
```
##### Ordering
```r
X[order(X$var1),]

X[order(X$var1, X$var3), ]
```
##### Ordering with plyr
```r
library(plyr)
arrange(X,var1)

arrange(X,desc(var1))
```
##### Adding rows and columns
```r
X$var4 <- rnorm(5)

Y <- cbind(X,rnorm(5))
Y
```
##### Summarizing Data
###### Getting data from the web
```r
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://data.baltimorecity.gov/api/views/k5ry-er3g/rows.csv?accessType=DOWNLOAD"
download.file( fileUrl, destfile="./data/restaurants.csv", method="curl")
restData <- read.csv("./data/restaurants.csv")
```
##### Look at a bit of data
```r
head(restData, n=3) # look at first 3 rows of restData plus header info

tail(restData, n=3) # look at last 3 rows of restData plus header info
```
##### Make summary
```r
summary(restData)
# gives an indepth summary of this dataset

str(restData) # even more indepth information
```
##### Quantiles of quantitative variables
```r
# shows 0, 25, 50, 75, and 100%
quantile( restData$councilDistrict, na.rm=TRUE )

# shows 50, 75, 80 %
quantile( restData$councilDistrict, probs=c(0.5,0.75,0.9 ))
```
##### making a table
```r
table( restData$zipCode, useNA="ifany" )

table( restData$councilDistrict, restData$zipCode )
```
##### Check for missing values
```r
sum( is.na( restData$councilDistrict ) )

any( is.na( restData$councilDistrict ) )

all( restData$zipCode > 0 )
```
##### Row and Column Sums
```r
colSums( is.na( restData ) )
all( colSums(is.na(restData))==0 )
```
##### Values with specific characteristics
```r
# gives true or false count
table( restData$zipCode %in% c("21212") )

# gives true or false count
table( restData$zipCode %in% c("21212", "21213") )

# gives subset of results
restData[ restData$zipCode %in% c("21212"), ]
```
##### Cross tabs
```r
data(UCBAdmissions)
DF = as.data.fram(UCBAdmissions)
summary(DF)

# gives 2 x 2 table of genders vs admissions
xt <- xtabs(Freq ~ Gender + Admit, data=DF )
xt
```
##### Flat tables
```r
warpbreaks$replicate <- rep(1:9, len=54)
xt = xtabs(breaks ~.,data=warpbreaks)

ftable(xt)
```
##### Size of a data set
```r
fakeData = rnorm(1e5)
object.size(fakeData)

# output
800040 bytes
#

print(object.size(fakeData),units="Mb")

# output
0.8mb
#
```