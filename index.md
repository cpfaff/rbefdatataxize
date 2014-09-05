---
title       : A workflow with
subtitle    : BEFdata + rBEFdata + taxize
author      : Claas-Thido Pfaff
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---




## Install packages

* rbefdata (interface to BEFdata)


```r
install.packages("rbefdata")
```

* taxize (taxonomy tasks)


```r
install.packages("taxize")
```

* Trim (leading/trailing spaces)


```r
install.packages("gdata")
# or use that one as trimmer:
trim <- function (x) gsub("^\\s+|\\s+$", "", x)
```

---

## List Options (rbefdata)



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

## Set Options (rbefdata)

- The URL ([Workshop Portal](http://stage.china.befdata.biow.uni-leipzig.de))


```r
bef.options(url = "http://stage.china.befdata.biow.uni-leipzig.de")
```

- The credentials ([Your profile](http://stage.china.befdata.biow.uni-leipzig.de/profile))


```r
bef.options(user_credentials = "y9H5uaC65fr1qCNdk0H1")
```

---
  
## Get data
  
* Get dataset from [proposal](http://stage.china.befdata.biow.uni-leipzig.de/paperproposals/1) with id #1 


```r
datasets = bef.portal.get.datasets.for_proposal(id = 1)
length(datasets)
```

```
## [1] 2
```

```r
titles = sapply(datasets, function(x) attributes(x)$title)
titles
```

```
## [1] "Dataset with errors in tree species" "How to fill a BEFdata Workbook"
```

```r
dataset = datasets[[1]]
```

---

## Inspect the data

* Inspect the dataset (metadata)
  

```r
attributes(dataset)$title
```

```
## [1] "Dataset with errors in tree species"
```

```r
names(attributes(dataset))
```

```
##  [1] "names"                    "class"                    "row.names"                "title"                    "abstract"                
##  [6] "publicationDate"          "language"                 "creators"                 "authors"                  "intellectualRights"      
## [11] "distribution"             "keywords"                 "generalTaxonomicCoverage" "samplingDescription"      "spatial_coverage"        
## [16] "temporal_coverage"        "related_material"         "columns"
```

---

## Inspect the data

* About the spces column and their description


```r
names(dataset)
```

```
## [1] "id"       "species"  "csp"      "den_bran" "den_core"
```

```r
attributes(dataset)$column$description
```

```
## [1] "CSP tree individuals were marked mostly with metal tags but also additional tags were used. Very few metal tag numbers were double across CSPs. For a unique ID across CSPs see the Data Group \"Tree identifier for trees in the comparative study sites\". Other projects might have used non metal tags with custom numbers. E. g., white tags might hold numbers defined for additional trees of the SP5 subproject. (id: id given in the cspCSP tree tag number)"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
## [2] "The scientific species fullnames are based on the \"Flora of China\" identified by Teng Fang and verified by Helge Bruelheide (trees) and Alexandra Efmeier (herbs). Scientific species names consist of epithet and genus. For identification, the Author name and the year of the publications of the description are required. (species: name of the species; Scientific plant species name;; ; Flora of China, Helge Bruelheide (derived from datagroup))"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
## [3] "Reasearch plots of the Biodiversity - Ecosystem functioning experiment (BEF-China). There are three main sites for research plots in the BEF Experiment: Comparative Study Plots (CSP) in the Gutianshan Nature Reserve, having a size of 30x30m^2, measured on the ground. Main Experiment plots have a size of 1 mu, which is about 25x25m^2 in horizontal projection. Pilot Study Plots have a size of 1x1 m^2. Research plots on the main experiment have a \"p\" in front of their IDs and then a 6 digit code: Plots in the main sites A and B are named according to their position in the original spreadsheet, in which they were designed. They consist of 6 digits: _1st digit_: Site (1:A, 2:B), _digit 2and3_: southwards row: as in spreadsheets the rows are named from the top to the bottom; _digit 4 and 5_: westward column: as in the original spreadsheet, but the letters are converted to numbers (A=01, B=02); _6th digit_: indicator, if the plot has been shifted a quarter mu. Example: \"p205260\": \"p\" means that this is a plot that is specified. \"2\" means, that we are at site B. Now the coordinates of the south - west corner: \"0526\". Since \"e\" is the fifth letter of the alphabet, this is Plot E26. The last digit \"0\" means that this plot was not moved by a quarter of a Mu, as some sites in Site A. The 6th digit can also indicate the subplot within the plot. \"5\", \"6\", \"7\", \"8\" indicate the northwest, northeast, southeast, and southwest quarter plot respectively. (csp: name of the CSP in the Nature ReserveBEF research plot nameReasearch plots of the Biodiversity - Ecosystem functioning experiment (BEF-China). There are three main sites for research plots in the BEF Experiment: Comparative Study Plots (CSP) in the Gutianshan Nature Reserve, having a size of 30x30m^2, measured on the ground. Main Experiment plots have a size of 1 mu, which is about 25x25m^2 in horizontal projection. Pilot Study Plots have a size of 1x1 m^2. Research plots on the main experiment have a \"p\" in front of their IDs and then a 6 digit code: Plots in the main sites A and B are named according to their position in the original spreadsheet, in which they were designed. They consist of 6 digits: _1st digit_: Site (1:A, 2:B), _digit 2and3_: southwards row: as in spreadsheets the rows are named from the top to the bottom; _digit 4 and 5_: westward column: as in the original spreadsheet, but the letters are converted to numbers (A=01, B=02); _6th digit_: indicator, if the plot has been shifted a quarter mu. Example: \"p205260\": \"p\" means that this is a plot that is specified. \"2\" means, that we are at site B. Now the coordinates of the south - west corner: \"0526\". Since \"e\" is the fifth letter of the alphabet, this is Plot E26. The last digit \"0\" means that this plot was not moved by a quarter of a Mu, as some sites in Site A. The 6th digit can also indicate the subplot within the plot. \"5\", \"6\", \"7\", \"8\" indicate the northwest, northeast, southeast, and southwest quarter plot respectively.; Datagroup description: Reasearch plots of the Biodiversity - Ecosystem functioning experiment (BEF-China). There are three main sites for research plots in the BEF Experiment: Comparative Study Plots (CSP) in the Gutianshan Nature Reserve, having a size of 30x30m^2, measured on the ground. Main Experiment plots have a size of 1 mu, which is about 25x25m^2 in horizontal projection. Pilot Study Plots have a size of 1x1 m^2.Research plots on the main experiment have a \"p\" in front of their IDs and then a 6 digit code: Plots in the main sites A and B are named according to their position in the original spreadsheet, in which they were designed. They consist of 6 digits: _1st digit_: Site (1:A, 2:B), _digit 2and3_: southwards row: as in spreadsheets the rows are named from the top to the bottom; _digit 4 and 5_: westward column: as in the original spreadsheet, but the letters are converted to numbers (A=01, B=02); _6th digit_: indicator, if the plot has been shifted a quarter mu. Example: \"p205260\": \"p\" means that this is a plot that is specified. \"2\" means, that we are at site B. Now the coordinates of the south - west corner: \"0526\". Since \"e\" is the fifth letter of the alphabet, this is Plot E26. The last digit \"0\" means that this plot was not moved by a quarter of a Mu, as some sites in Site A. The 6th digit can also indicate the subplot within the plot. \"5\", \"6\", \"7\", \"8\" indicate the northwest, northeast, southeast, and southwest quarter plot respectively.)"
## [4] "Wood density (den_bran: wood density of one branch; ;)"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
## [5] "Wood density (den_core: wood density of one stem wood core; ;)"
```

---

## Species names

* Prepare species list


```r
require(gdata)
# or define trimmer
trim <- function (x) gsub("^\\s+|\\s+$", "", x)
species = trim(dataset$species)
unique_species = unique(as.character(species))
head(unique_species, 7)
```

```
## [1] "Acer amplum"         "Acer cdatum"         "Acer cordatum"       "Acer pubipalmatum"   "Adinandra millettii" "Adinandra milletti" 
## [7] "Adinandra milletii"
```

* Load ([taxize](http://ropensci.org/tutorials/taxize_tutorial.html))


```r
require(taxize)
```

---

## Species names

* Taxize offers access to databases like: 
    - Integrated Taxonomic Information System [itis](http://www.itis.gov/) 
    - National Center for Biotechnology Information [ncbi](http://www.ncbi.nlm.nih.gov/) 
    - International Union for Conservation of Nature [iucn](http://www.iucn.org/)

* But that is not all (e.g create phylogeny tree, see [tutorial](http://ropensci.org/tutorials/taxize_tutorial.html))

* Example for species resolving


```r
species_list = c("Helianthus annuus", "Pinus contort")
query_response = tnrs(query = species_list, source = "iPlant_TNRS")[, -c(6:7)]
query_response
```

```
##       submittedname      acceptedname    sourceid score       matchedname
## 1     Pinus contort    Pinus contorta iPlant_TNRS  0.98    Pinus contorta
## 2 Helianthus annuus Helianthus annuus iPlant_TNRS     1 Helianthus annuus
```

--- bg:#EEE

## Your turn

* Start R-Studio
* Load packages

```
require(rbefdata)
require(taxize)
```

* Set options (URL, Credentials)

```
bef.options()
```

* Resolve all the species in vector unique_species (unique)


```r
tnrs(query = ...)
```

* Find the entries that need correction (Hint: score! with subset())

---

## A solution 

* Check the unique names of our species list


```r
query_response = tnrs(query = unique_species)[, -c(6:7)]
head(query_response,9)[c("submittedname", "acceptedname", "score")]
```

```
##              submittedname             acceptedname score
## 1              Acer amplum              Acer amplum     1
## 2 Camellia chekiang-oleosa  Camellia chekiangoleosa  0.99
## 3         Cleyera japonica         Cleyera japonica     1
## 4          Albizia kalkora          Albizia kalkora     1
## 5 Castanopsis sclerophylla Castanopsis sclerophylla     1
## 6     Castanopsis fargesii     Castanopsis fargesii     1
## 7        Camellia oleifera        Camellia oleifera     1
## 8 Camellia chekiang-oleosa  Camellia chekiangoleosa     1
## 9              Acer cdatum            Acer caudatum  0.94
```

--- 

## A solution 

* Pick the entries that are lower than 1


```r
query_response_need_attention = unique(subset(query_response, score < 1))
head(query_response_need_attention, 7)
```

```
##                 submittedname            acceptedname    sourceid score             matchedname
## 2    Camellia chekiang-oleosa Camellia chekiangoleosa iPlant_TNRS  0.99 Camellia chekiangoleosa
## 9                 Acer cdatum           Acer caudatum iPlant_TNRS  0.94           Acer caudatum
## 14 Chlerodendron cyrtophyllum            Clerodendrum iPlant_TNRS   0.5            Clerodendron
## 31         Adinandra milletti     Adinandra millettii iPlant_TNRS  0.98     Adinandra millettii
## 45            Camellia frerna       Camellia fraterna iPlant_TNRS  0.94       Camellia fraterna
## 84      Cunninghamia laeolata Cunninghamia lanceolata iPlant_TNRS  0.96 Cunninghamia lanceolata
## 91              Eurya murcata          Eurya muricata iPlant_TNRS  0.98          Eurya muricata
```

--- 

## Match back and combine

* Match species to submitted names


```r
corrected_dataset = merge(dataset, 
                          query_response_need_attention[c("submittedname", "acceptedname")], 
                          by.x = "species", 
                          by.y = "submittedname", all.x = T)

head(corrected_dataset)
```

```
##         species     id csp den_bran den_core  acceptedname
## 1   Acer amplum 369192  18   0.5261       NA          <NA>
## 2   Acer amplum 363008   2   0.5220       NA          <NA>
## 3   Acer amplum 372621   4       NA   0.5589          <NA>
## 4   Acer cdatum 379787   2   0.2862       NA Acer caudatum
## 5 Acer cordatum 371236   4   0.8426   0.3427          <NA>
## 6 Acer cordatum 376888  10   1.1964       NA          <NA>
```

---

## Match back and combine 

* merge function picks from vectors (if else)


```r
merge_em <- function(base_vec, merge_vec) {
  output = vector()
  for(i in 1:length(base_vec)) {
    if(is.na(base_vec[i])) {
      output[i] = merge_vec[i]
    } else {
      output[i] = base_vec[i]
    }
  }
  return(output)
}
```

---

## Match back and combine 

* Define base and merge vector


```r
base = corrected_dataset$acceptedname
mergein = as.character(species)
new = base[is.na(base)] <- mergein[is.na(base)]
```

---

## Match back and combine

* create new dataframe


```r
merged_names = merge_em(base_vec = base, merge_vec = mergein)
corrected_dataset = data.frame(dataset, species_corrected = merged_names)
head(corrected_dataset, 8)
```

```
##       id           species csp den_bran den_core species_corrected
## 1 369192       Acer amplum  18   0.5261       NA       Acer amplum
## 2 363008       Acer amplum   2   0.5220       NA       Acer amplum
## 3 372621       Acer amplum   4       NA   0.5589       Acer amplum
## 4 379787       Acer cdatum   2   0.2862       NA     Acer caudatum
## 5 371236     Acer cordatum   4   0.8426   0.3427     Acer cordatum
## 6 376888     Acer cordatum  10   1.1964       NA     Acer cordatum
## 7 363098     Acer cordatum  13       NA   1.0376     Acer cordatum
## 8 377795 Acer pubipalmatum  11   0.2244       NA Acer pubipalmatum
```

---

## Upload dataset

* push back a corrected version to BEFdata

* Please use own title if you execute this step 


```r
title = "Dataset with errors in tree species (corrected)"
bef.portal.upload.dataset(dataset = corrected_dataset, dataset_title = title)
```

```
Your data has been uploaded successfully!
You can find your dataset now under the id:3
```

---

## Get higher taxonomic names

* Use taxize to retrive higher taxon names

* Create a new dataframe 
     - corrected unique species names 
     - order
     - family
     - genus

* Can be used to calculate phylogenetic diversity

---

## Get higher taxonomic names

* Example on retrieving classifications


```r
species_examples = c("Homo sapiens", "Rattus rattus")
```

* The databases can be e.g. [itis](http://www.itis.gov/) or [ncbi](http://www.ncbi.nlm.nih.gov/)


```r
classification_list = classification(species_examples, db = "itis")
classification_list[[1]]
```

```
##             name         rank
## 1       Animalia      Kingdom
## 2      Bilateria   Subkingdom
## 3  Deuterostomia Infrakingdom
## 4       Chordata       Phylum
## 5     Vertebrata    Subphylum
## 6  Gnathostomata  Infraphylum
## 7      Tetrapoda   Superclass
## 8       Mammalia        Class
## 9         Theria     Subclass
## 10      Eutheria   Infraclass
## 11      Primates        Order
## 12   Haplorrhini     Suborder
## 13   Simiiformes   Infraorder
## 14    Hominoidea  Superfamily
## 15     Hominidae       Family
## 16     Homininae    Subfamily
## 17          Homo        Genus
## 18  Homo sapiens      Species
```

---

## Get higher taxonomic names

* Get only parts of intrest


```r
tax_name(query = "Homo Sapiens", get = "genus", db = "ncbi")
```

```
##   genus
## 1  Homo
```

```r
tax_name(query = species_examples, get = "family", db = "ncbi")
```

```
##      family
## 1 Hominidae
## 2   Muridae
```

--- bg:#EEE

## Your turn

* Get the order, family and genus from ncbi for unique corrected species
    
* Be nice and only query server once!
    - do not use: tax_name(...)
    - use classification(...)

* Hint: use R apply functions
    
* When finished upload the dataset to the portal!

---

## A solution

* Get: 
    - order, family and genus (unique corrected species use "ncbi")


```r
species_list = unique(as.character(corrected_dataset$species_corrected))
classification_species = classification(species_list, db = "ncbi")
```

* Extract (order):


```r
extraction_ncbi_order = sapply(classification_species, function(x) { 
   if(length(x) != 2){NA}else{subset(x, x$"rank" == "order")}$"name"
  })
```

---

## A solution

* Extract (family):


```r
extraction_ncbi_family = sapply(classification_species, function(x) {
   if(length(x) != 2){NA}else{subset(x, x$"rank" == "family")}$"name" 
  }) 
```

* Extract (genus):


```r
extraction_ncbi_genus = sapply(classification_species, function(x) { 
   if(length(x) != 2){NA}else{subset(x, x$"rank" == "genus")}$"name"
  })
```

---

## A solution

* Create data frame:


```r
extraction_ncbi_order_fam_genus = cbind(extraction_ncbi_order, 
                                        extraction_ncbi_family, 
                                        extraction_ncbi_genus)
species_taxonic = data.frame(species = species_list, extraction_ncbi_order_fam_genus)
head(species_taxonic, 5)
```

```
##                                 species extraction_ncbi_order extraction_ncbi_family extraction_ncbi_genus
## Acer amplum                 Acer amplum                    NA                     NA                    NA
## Acer caudatum             Acer caudatum            Sapindales              Aceraceae                  Acer
## Acer cordatum             Acer cordatum            Sapindales              Aceraceae                  Acer
## Acer pubipalmatum     Acer pubipalmatum                    NA                     NA                    NA
## Adinandra millettii Adinandra millettii              Ericales       Pentaphylacaceae             Adinandra
```

---

## A solution

* Upload:


```r
bef.portal.upload.dataset(dataset = species_taxonic, dataset_title = "Taxonomic information for species in ...")
```

```
Your data has been uploaded successfully!
You can find your dataset now under the id:3
```

---

## Thanks four your attention 
