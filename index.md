---
title       : A workflow with
subtitle    : BEFdata + rBEFdata + taxize
author      : Claas-Thido Pfaff
job         : Also showcasing other useful tools (plyr)
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---




## Prerequisites

* Packages


```r
install.packages("rbefdata")
```


```r
install.packages("taxize")
```


```r
install.packages(c("gdata", "plyr"))
```

* String cleaning helpers


```r
# leading/trailing spaces
trim <- function (x) gsub("^\\s+|\\s+$", "", x)
# multiple spaces
squish <- function(x) gsub("\\s+", " ", x)
```

---

## List options (rbefdata)


```r
require(rbefdata)
bef.options()
```

```
## $url
## [1] "http://china.befdata.biow.uni-leipzig.de"
## 
## $tematres_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/index.php"
## 
## $tematres_service_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/services.php"
## 
## $download_dir
## [1] "downloads"
## 
## $user_credentials
## [1] ""
```

---

## Set options (rbefdata)

- The URL ([Workshop Portal](http://stage.china.befdata.biow.uni-leipzig.de))
  - Which server to communicate with
  - Here you would set your own URL


```r
bef.options(url = "http://stage.china.befdata.biow.uni-leipzig.de")
```

- The credentials ([Your profile](http://stage.china.befdata.biow.uni-leipzig.de/profile))
  - Needed for authentication
  - At least for restricted datasets


```r
bef.options(user_credentials = "Pcz3lXF82FDULZP2GG29")
```

---

## Set options (rbefdata)


```r
bef.options()
```

```
## $url
## [1] "http://stage.china.befdata.biow.uni-leipzig.de"
## 
## $tematres_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/index.php"
## 
## $tematres_service_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/services.php"
## 
## $download_dir
## [1] "downloads"
## 
## $user_credentials
## [1] "Pcz3lXF82FDULZP2GG29"
```

--- bg:#EEE

## Your turn

* Fire up R-Studio
* Load the `rbefdata` package


```r
require(rbefdata)
```

* Get your [credentials](http://stage.china.befdata.biow.uni-leipzig.de/profile)
  - Check your profile page

* Set the [URL](stage.china.befdata.biow.uni-leipzig.de) to the BEFdata server (`bef.options()`)
  - Check the URL of the BEFdata instance
  - `bef.options(...)`

* Set the Credentials 
  - `bef.options(...)`

---

## Get data


```r
# the id can be found in the url of the dataset
bef.portal.get.dataset_by(id = xx)
```

* Get dataset from the [portal](http://stage.china.befdata.biow.uni-leipzig.de/datasets/6) with id #6


```r
dataset = bef.portal.get.dataset_by(id = 6)
```


```r
head(dataset, 4)
```


|SpeciesFull         | Plot| Carbon| Nitrogen|
|:-------------------|----:|------:|--------:|
|Acer cordatum       |    2|  48.11|     0.50|
|Acer cordatum       |   13|  47.83|     0.33|
|Acer cordatum       |   13|  47.49|     0.48|
|Adinandra millettii |    1|  49.91|     0.59|

---

## Inspect the data

* Inspect data (metadata)
  

```r
attributes(dataset)$title
```

```
## [1] "Carbon and nitrogen measurements (example)"
```

```r
names(attributes(dataset))
```

```
##  [1] "names"                    "class"                    "row.names"               
##  [4] "title"                    "abstract"                 "publicationDate"         
##  [7] "language"                 "creators"                 "authors"                 
## [10] "intellectualRights"       "distribution"             "keywords"                
## [13] "generalTaxonomicCoverage" "samplingDescription"      "spatial_coverage"        
## [16] "temporal_coverage"        "related_material"         "columns"
```

---

## Inspect data

* Get detailed information
  - On the columns


```r
names(dataset)
```

```
## [1] "SpeciesFull" "Plot"        "Carbon"      "Nitrogen"
```

```r
attributes(dataset)$column$description
```

```
## [1] "The species full name (SpeciesFull: Species Full Scientific Name)"               
## [2] "The plot number (Plot: Plot Number)"                                             
## [3] "Carbon concentration measurement (Carbon: Carbon Concentration Of Species)"      
## [4] "Nitrogen concentration measurement (Nitrogen: Nitrogen Concentration Of Species)"
```

--- bg:#EEE

## Your turn

* Get the dataset with the id = 6
  - use `bef.portal.get.dataset_by(id)`
  - Save it to a variable 

* Inspect the dataset metadata
  - find the creator and save it to a variable
  - find the intellectual rights and save it to a variable
  - hint: use `attributes()` and `names()`

--- bg:#EEE

## Your turn (solution)

* Download


```r
dataset = bef.portal.get.dataset_by(id = 6)
```

* Creator and intellectual rights


```r
attributes(dataset)$creators
```

```
##     givenName surName            electronicMailAddress
## 1 Claas-Thido   Pfaff claas-thido.pfaff@uni-leipzig.de
```

```r
attributes(dataset)$intellectualRights
```

```
## [1] "CCBYSA"
```

---

## Aggregate (plyr)

* Load the package


```r
require(plyr)
```

* Syntax


```r
ddply(data.frame, variable(s), function, optional arguments)
# summarise vs transform
```

* Clean the column


```r
# clean the species column 
dataset$SpeciesFull = trim(dataset$SpeciesFull)
dataset$SpeciesFull = squish(dataset$SpeciesFull)
```

---

## Aggregate (plyr)

* Aggregate by Species full calculate mean c/n ratio


```r
aggregated_dataset = ddply(dataset, .(SpeciesFull), summarise, 
                           CarbonNitrogenRatio = mean(Carbon/Nitrogen))
```

* Lets have a look


```r
head(aggregated_dataset, 4)
```


|SpeciesFull         | CarbonNitrogenRatio|
|:-------------------|-------------------:|
|Acer cordatum       |           113.36563|
|Adinandra millettii |           168.52947|
|Alangium kurzii     |            49.62674|
|Albizia kalkora     |            71.71055|

---

## Use taxize (taxonomic tool-belt)

* Taxize offers access to various databases
    - Integrated Taxonomic Information System [itis](http://www.itis.gov/) 
    - National Center for Biotechnology Information [ncbi](http://www.ncbi.nlm.nih.gov/) 
    - International Union for Conservation of Nature [iucn](http://www.iucn.org/)

* But that is not all (e.g create phylogeny tree, see [tutorial](http://ropensci.org/tutorials/taxize_tutorial.html))


* Load ([taxize](http://ropensci.org/tutorials/taxize_tutorial.html))


```r
require(taxize)
```

* e.g. retrieve higher taxonomic names, synonyms and many more

---

## Use taxize (taxonomic tool-belt)

* Get family names


```r
ncbi_family_names = tax_name(query = aggregated_dataset$SpeciesFull, 
                             get = "family", 
                             db = "ncbi", 
                             verbose = F)
```

* Lets check that out


```r
head(ncbi_family_names, 3)
```


|db   |query               |family           |
|:----|:-------------------|:----------------|
|ncbi |Acer cordatum       |Aceraceae        |
|ncbi |Adinandra millettii |Pentaphylacaceae |
|ncbi |Alangium kurzii     |Cornaceae        |

---

## Use taxize (taxonomic tool-belt)

* Combine


```r
aggregated_dataframe_family = data.frame(aggregated_dataset, 
                                         FamilyNames = ncbi_family_names$family)
```


|SpeciesFull         | CarbonNitrogenRatio|FamilyNames      |
|:-------------------|-------------------:|:----------------|
|Acer cordatum       |            113.3656|Aceraceae        |
|Adinandra millettii |            168.5295|Pentaphylacaceae |

* Merge


```r
merged_dataset_family = merge(dataset, aggregated_dataframe_family[c(1,3)], by = "SpeciesFull")
```


|SpeciesFull   | Plot| Carbon| Nitrogen|FamilyNames |
|:-------------|----:|------:|--------:|:-----------|
|Acer cordatum |    2|  48.11|     0.50|Aceraceae   |
|Acer cordatum |   13|  47.83|     0.33|Aceraceae   |

--- bg:#EEE

## Your turn

* Use `unique()` species names to retrieve the family 
  - use the function `tax_name` from the taxize package
  - set the parameters `get="family"`, `db="ncbi"`
* Merge family names back to the downloaded dataframe 
  - use `merge()` function
* Aggregate a mean carbon/nitrogen ratio on family level
  - Use `ddply(dataset, .(ColumnName), function, newColumn = ...)` function 
  - Try out summarise vs transform as function in the ddply call

---

## Solution


```r
unique_species = unique(dataset$SpeciesFull)
ncbi_family_names = tax_name(query = unique_species, 
                             get = "family", 
                             db = "ncbi", verbose = F)
names(ncbi_family_names) = c("db", "SpeciesFull", "FamilyNames")
merged_dataset_family = merge(dataset, ncbi_family_names[c(2,3)], by = "SpeciesFull")
aggregated_dataset = ddply(merged_dataset_family, .(FamilyNames), summarise, 
                           CarbonNitrogenRatio = mean(Carbon/Nitrogen))
```



|FamilyNames   | CarbonNitrogenRatio|
|:-------------|-------------------:|
|Aceraceae     |           113.36563|
|Adoxaceae     |            76.37549|
|Altingiaceae  |           148.41349|
|Anacardiaceae |            87.52555|

---

## Upload dataset (rBEFdata)

* Upload the data to the portal


```r
# provide a dataset and title
bef.portal.upload.dataset(...)
```

* Upload the aggregated carbon/nitrogen dataset 


```r
title = "Carbon nitrogen ratio by family (claas)"
bef.portal.upload.dataset(dataset = aggregated_dataset, dataset_title = title)
```

```
Your data has been uploaded successfully!
You can find your dataset now under the id:7
```

--- bg:#EEE

## Your turn

* Upload the new dataset to the BEFdata server
  - Provide an own unique title (Maybe add your name in the title) 
  - use: `bef.portal.upload.dataset(dataset = , dataset_title = )

---

## Thanks four your attention
