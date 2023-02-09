---
title: "Assessment 5 - Text Wrangling"
author: "52414"
date: "May 23rd, 2020"
output: 
  html_document: 
    keep_md: yes
---


**Data**
In this assessment we will load and analyze a dataset of Greta Thunberg's tweets. Make sure that the datafile 'greta.csv' is in the same directory of your Rmd file to properly load the data.  


<img src="https://upload.wikimedia.org/wikipedia/commons/4/47/Greta_Thunberg_urges_MEPs_to_show_climate_leadership_%2849618310531%29_%28cropped%29.jpg"  width="255" height="350">




**Some Useful Functions**

* `str_detect()` detects the presence or absence of a pattern and returns a logical vector (similar to `grepl()`).  
* `str_count()` counts the number of matches  
* `str_locate()` locates the first position of a pattern and returns a numeric matrix with columns start and end. <br>
`str_locate_all()` locates all matches, returning a list of numeric matrices.  
* `str_extract()` extracts text corresponding to the first match, returning a character vector. <br> `str_extract_all()` extracts all matches and returns a list of character vectors.  
* `str_match()` extracts capture groups formed by () from the first match.  
* `str_replace()` replaces the first matched pattern and returns a character vector. `str_replace_all()` replaces all matches. Similar to `sub()` and `gsub()`.
* `str_subset()` returns the elements of a character vector that match a regular expression (similar to `grep()` with value = TRUE)`.  
* In most cases the pattern these functions accept can be either a string or a regex.  
* You can access individual character using `str_sub()`. It takes three arguments: a character vector, a start position and an end position.

**Useful Resources**

Check out these `regex` cheat-sheets [1](https://rstudio.com/wp-content/uploads/2016/09/RegExCheatsheet.pdf)
[2](https://evoldyn.gitlab.io/evomics-2018/ref-sheets/R_strings.pdf)
that will help you come up with the right regex for the exercises below.  

In addition, check out this online regex checker to test your `regex` [online](https://regexr.com/).  

**Note:** *In R, it is usually required to put double escape characters. In some other regex languages a single escape character is enough. For example: to denote a digit use '\\\\d' and not  '\\d'*.


**Questions**

1. Regex Practice Questions:

a. Use `str_detect` with the appropriate regex to find how many tweets start with proper english grammar: <br>
The first word starts with a capital english letter appearning exactly once (no other capital letters immediately follow it).   

```r
proper_gramar <- as.data.frame(greta$text %>% str_detect("^[:upper:](?=[:lower:])"))
proper_gramar <- proper_gramar %>% mutate(c = ifelse(proper_gramar==TRUE,1,0))
print(sum(proper_gramar$c))
```

```
## [1] 2162
```

b. Using `str_detect`, count the number of tweets in which the word 'climate' appears, *including* appearances as 'Climate' (e.g. at the start of a sentence) :


```r
is_climate <- as.data.frame(greta$text %>% str_detect("(climate|Climate)"))
is_climate <- is_climate %>% mutate(c = ifelse(is_climate==TRUE,1,0))
print(sum(is_climate$c))
```

```
## [1] 2120
```

c. Using `str_extract`, print all the hashtags ('#') that start with 'Z' (for example: '#Zero') appearing in Greta's tweets. 


```r
hash_with_z <- greta$text %>% str_extract_all("#(Z|z)[:alpha:]+",simplify = T)
fir <- hash_with_z[,1]
fir2 <-hash_with_z[,2] 
ro <- fir[which(fir!="")]
ro2 <- fir2[which(fir2!="")]
print(ro)
```

```
##  [1] "#zerowaste"         "#Zeugnistag"        "#zeist"            
##  [4] "#ZeroFossile"       "#zerowaste"         "#ZielonyPatriotyzm"
##  [7] "#ZeroCarbon"        "#ZeroHour"          "#zurich"           
## [10] "#Zurich"
```

```r
print(ro2)
```

```
## [1] "#ZieloneRosnie"
```

d. We're interested in tracking mentions of temperature at different years in Greta's tweets. Using `str_extract`, extract and print every tweet that contains as a word or part of word one of the following words: 'temperature, hot, cold, warm', followed (not neccessarily consecutively) by a year between $1800$ and $2099$: 


```r
gerta_temp <- greta$text %>% str_extract(".+(temperature|hot|cold|warm).+(18|19|20)\\d{2}.+")
gerta_temp<-as.data.frame(gerta_temp)
gerta_temp<-na.omit(gerta_temp)
gerta_temp <- gerta_temp[-2,]
print(gerta_temp)
```

```
##  [1] NASA: April was the warmest on record, globally: +1.16°C above the 1951-1980 normal, ahead of 2016 and 2019.                                                                                                                                                                                                
##  [2] Wow, January 2020 was the warmest on record and greater than 3°C above the 1981-2010 climate average in Europe. The average global temperature was also the highest on record. <U+0001F525>                                                                                                                 
##  [3] Global temperature change (1850-2019). #showyourstripes https://t.co/nXC9wGRa2x                                                                                                                                                                                                                             
##  [4] Ocean heat content in 2019 was the warmest on record by a sizable margin. Between 2018 and 2019 the oceans absorbed an amount of heat around four times larger than all the energy used by humans in the world. https://t.co/hDWeOG5Rz3 https://t.co/moHfhn5Z5a                                             
##  [5] “2019 was a very hot year, with global average temperatures the second highest on record. Only 2016 was hotter, and not by much — less than one-tenth of a degree Fahrenheit.” https://t.co/0KmvbQz3zL                                                                                                      
##  [6] History of national-average temperature and precipitation since 1910. https://t.co/aHh3kDFIZ7                                                                                                                                                                                                               
##  [7] November 2019 was the warmest on record globally, together with 2016, and just ahead of 2015: +0.64°C above its 1981-2010 normal. Extremely mild in Alaska, Greenland and Eastern Siberia. Warm in SE-Europe, cool in E-US and W-Asia. [EU analysis] https://t.co/9BDQVpFMvt                                
##  [8] #Arctic air temperature rank by month over the satellite era - now updated through October 2019...                                                                                                                                                                                                          
##  [9] <U+0001F534><U+0001F321><U+FE0F> Last month's temperatures were 0.69°C above average, making #October 2019 the hottest on record. As can be seen in the picture, large parts of the #Arctic, most of #Europe, the eastern #USA and #Canada were most affected.                                              
## [10] Historic windstorms, the hottest temperatures of 2019, extreme wildfires, and mass blackouts to prevent those wildfires are all inflicting California this week. We are in a #ClimateEmergency                                                                                                              
## [11] Note that the warming numbers given by IPCC are up to the mean over 2006-2015, not up to the present. If you remove interannual noise by a low-pass filter (or just eyeballing) you see that land areas have warmed about 1.8 °C until today, and global mean temperature by 1.1 °C. https://t.co/huSZTUZU4H
## [12] Wow, June 2019 was the hottest on record and greater than 2°C above the 1981-2010 climate average in Europe. The average global temperature was also the highest on record.                                                                                                                                 
## [13] #Arctic air temperature rank by month over the satellite era - May 2019 is the highest on record in this analysis                                                                                                                                                                                           
## [14] NASA: April 2019 was the 2nd warmest on record globally, behind 2016: +0.99°C above the 1951-1980 average.                                                                                                                                                                                                  
## [15] Well back in '82 @ExxonMobil predicted the rise in CO2 &amp; temperature we'd see by 2019 from ongoing fossil fuel burning.                                                                                                                                                                                 
## [16] Wait for it. Global temperature from 1861 to 2018. Notice a pattern?                                                                                                                                                                                                                                        
## 17 Levels: #Arctic air temperature rank by month over the satellite era - May 2019 is the highest on record in this analysis ...
```

2. Using `str_replace` and additional commands, convert all times from 24-hour format to AM/PM  times. For example: 
'2020-05-22 20:00:14' should be '2020-05-22 8:00:14PM' and '2020-05-22 08:12:21' should be '2020-05-22 8:12:21AM'. Print the first $21$ times after replacement. Pay attention that: (i) leading zeros shouldn't appear after the format change (ii)  '12' should convert to '12 PM' and '00' should convert to '12 AM'. 



```r
greta$created_at <- greta$created_at %>%  str_replace(pattern = greta$created_at,replacement = format(strptime(greta$created_at,"%Y-%m-%d %H:%M:%S"),"%Y-%m-%d %I:%M:%S %p"))

head(greta$created_at,21)
```

```
##  [1] "2020-05-22 08:00:14 PM" "2020-05-22 07:59:22 PM" "2020-05-22 06:49:14 PM"
##  [4] "2020-05-22 06:19:59 PM" "2020-05-22 06:18:44 PM" "2020-05-22 06:18:23 PM"
##  [7] "2020-05-22 06:17:55 PM" "2020-05-22 01:22:54 PM" "2020-05-22 12:51:34 PM"
## [10] "2020-05-22 12:08:01 PM" "2020-05-22 11:50:32 AM" "2020-05-22 11:50:02 AM"
## [13] "2020-05-22 11:49:42 AM" "2020-05-22 08:12:21 AM" "2020-05-22 07:27:41 AM"
## [16] "2020-05-22 06:26:19 AM" "2020-05-21 05:21:19 PM" "2020-05-21 07:04:16 AM"
## [19] "2020-05-21 06:41:13 AM" "2020-05-20 08:00:59 PM" "2020-05-20 08:00:17 PM"
```


3. Make a word-cloud of Greta's tweets. Remove all common stop words (use the command `stop_words` from the *tidytext* package), and also all hashtages ('words' starting with '#') and twitter user names
('words' starting with '@'). Display in a word-cloud the top-100 (most-common) remaining words. 
 

```r
library(wordcloud2)
greta$text<-str_to_lower(greta$text)
wordi <- stop_words
word <- paste0(wordi$word,c(rep("\\b",length(wordi$word))))
word <- paste0(c(rep("\\b",length(wordi$word))),word)
greta$text <- greta$text %>% str_replace_all("<.+>","")
greta$text <- greta$text %>% str_replace_all("#.+\\b","")
greta$text <- greta$text %>% str_replace_all("@.+\\b","")
greta$text <-  greta$text %>% str_replace_all("https.+\\b","")
greta$text <- greta$text %>% str_replace_all(paste(word,collapse = "|"),"")
greta$text <- greta$text %>% str_replace_all("(\\.|\\'|\\,|\\!|\\?|\\~|\\-|\\”|\\�|\\’|\\&amp)","")
greta$text <- greta$text %>% str_replace_all("york","new-york")
greta$text <- greta$text %>% str_replace_all("\\s+[[:digit:]]+\\s+","")
greta$text <- greta$text %>% str_replace_all("[:punct:](?![:alpha:]+)","")
t <- strsplit(greta$text,"\\s+")
t<-table(unlist(t))
t<-as.data.frame(t)
t<-t[-1,]
t <- t %>% arrange(desc(t$Freq))
top_t <-t[c(1:100),]
wordcloud2(top_t)
```

```{=html}
<div id="htmlwidget-9ec7537de529a392b33c" style="width:672px;height:480px;" class="wordcloud2 html-widget"></div>
<script type="application/json" data-for="htmlwidget-9ec7537de529a392b33c">{"x":{"word":["climate","strike","week","people","school","world","change","future","crisis","global","time","join","action","day","wir","striking","de","planet","friday","die","greta","en","la","students","city","der","emergency","emissions","thunberg","youth","ve","science","australia","children","leaders","ll","act","stop","co2","heute","und","fight","och","support","activists","parliament","record","tomorrow","streets","don","government","justice","ecological","stand","strikes","demand","listen","news","thousands","new-york","speech","countries","pו","strikers","bangladesh","continue","el","home","joined","sind","south","auch","earth","fossil","front","india","morning","north","activist","house","live","sydney","vi","�","coming","demanding","ocean","stockholm","air","att","auf","average","human","media","protest","september","share","supporting","uganda","yesterday"],"freq":[650,435,198,196,162,155,154,136,132,116,105,99,96,80,80,77,76,70,65,64,64,63,61,59,58,57,57,56,56,56,54,53,52,51,50,49,48,47,46,46,43,42,42,42,41,41,41,41,40,39,38,36,35,35,35,34,34,33,33,32,32,30,30,30,28,28,28,28,28,28,28,27,27,27,27,27,27,27,26,26,26,26,26,25,25,25,25,25,24,24,24,24,24,24,24,24,24,24,24,24],"fontFamily":"Segoe UI","fontWeight":"bold","color":"random-dark","minSize":0,"weightFactor":0.276923076923077,"backgroundColor":"white","gridSize":0,"minRotation":-0.785398163397448,"maxRotation":0.785398163397448,"shuffle":true,"rotateRatio":0.4,"shape":"circle","ellipticity":0.65,"figBase64":null,"hover":null},"evals":[],"jsHooks":{"render":[{"code":"function(el,x){\n                        console.log(123);\n                        if(!iii){\n                          window.location.reload();\n                          iii = False;\n\n                        }\n  }","data":null}]}}</script>
```

