# Reddit Wordclouds with Python and R
Brian High  
2015-05-14  

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

## Get word frequencies

To make a simple wordcloud of a Reddit forum ("subreddit"), we can use 
`word_freqs` from the [reddit-analysis](https://github.com/rhiever/reddit-analysis) 
package to generate an output file of word frequencies. It uses the Reddit 
API to get data from the Reddit website.


```r
# Install reddit-analysis first: https://github.com/rhiever/reddit-analysis
# Replace USER with your own Reddit user name.
if (! file.exists("subreddit-publichealth.csv")){
    system("word_freqs /u/USER /r/publichealth", intern=TRUE)
}
```

## Install and load packages

We will install packages only if we don't already have them.


```r
for (pkg in c("RColorBrewer", "wordcloud")) {
    if (! require(pkg, character.only=TRUE)) { 
        install.packages(pkg, repos="http://cran.fhcrc.org", dependencies=TRUE)
        suppressPackageStartupMessages(library(pkg, character.only=TRUE))
    }
}
```

```
## Loading required package: RColorBrewer
## Loading required package: wordcloud
```

## Wordcloud function

It is very easy to make a wordcloud from the `word_freqs` output. Just import 
the data into a `data.frame` and run the `wordcloud` function on the word and 
frequency columns.

We will set a few other options with `par` and `title` to make the clouds nicer.

Wrapping this in a function will allow us to make clouds quickly and easily.


```r
wc <- function(subr){
    # Import only the first 100 rows (top 100 words)
    data <- read.table(file = paste0('subreddit-', subr, '.csv', collapse=''), 
                        header = FALSE, sep = ':', stringsAsFactors = FALSE, 
                        col.names = c("word", "freq"), nrows=100)

    # Use a black background, large bold white title text, and serif font
    par(bg = "black", col.main = "white", family = "serif", 
        cex.main = 2, font.main = 2)

    # Use scale= to limit the size of the words so they will fit in the cloud
    wordcloud(data$word, data$freq, colors=brewer.pal(12, "Set3"), 
              random.color = TRUE, scale=c(2.60, 0.70))

    # Set the title to the subreddit name
    title(paste0('/ r / ', subr, collapse=''))
}
```

## /r/publichealth

Try our first wordcloud.


```r
wc("publichealth")
```

![](reddit-wordcloud_files/figure-html/unnamed-chunk-4-1.png) 

## More, more!

How about "bioinformatics", "datascience", "dataisbeautiful", python", "rstats", 
"learnprogramming", and "programming"?


```r
subs <- c("bioinformatics", "datascience", "dataisbeautiful", "python", 
          "rstats", "learnprogramming", "programming")
```

Running `word_freqs` for all of these "subs" could take awhile...


```r
# Install reddit-analysis first: https://github.com/rhiever/reddit-analysis
# Replace USER with your own Reddit user name.
for (subr in subs) {
    if (! file.exists(paste0(c("subreddit-", subr, ".csv"), collapse=""))) {
        system(paste0(c("word_freqs /u/USER /r/", subr), collapse=""), intern=TRUE)
    }    
}
```

We can generate all of them at once using `sapply`


```r
res <- sapply(subs, wc)
```

![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-1.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-2.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-3.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-4.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-5.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-6.png) ![](reddit-wordcloud_files/figure-html/unnamed-chunk-7-7.png) 
