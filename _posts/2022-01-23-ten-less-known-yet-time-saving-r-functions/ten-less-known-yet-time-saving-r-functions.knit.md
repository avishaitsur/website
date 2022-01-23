---
title: "Ten less known yet time-saving tidyverse functions"
description: |
  Easy to miss, hard to forget.
author:
  - name: Avishai M Tsur
    url: {}
date: 2022-01-23
output:
  distill::distill_article:
    self_contained: false
---



<div class="layout-chunk" data-layout="l-body">
![](https://github.com/allisonhorst/allisonhorst/raw/main/horst_rtist.png)<!-- -->

</div>



# Introduction

Throughout the years, we become more fluent using R. However, like in any language, use of accurate words (functions in this case) can shorten, clarify, and save a lot of time in communicating code or insights.

The purpose of this post is to summarize a few functions learned the hard way, to be used a reference for me, and maybe allow you to discover some new function or new use cases for functions you already know.

# Data

For demonstrating purposes, we will use data from the tidytuesday project regarding Chocolate Ratings.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>chocolate</span> <span class='op'>&lt;-</span> <span class='fu'>readr</span><span class='fu'>::</span><span class='fu'><a href='https://readr.tidyverse.org/reference/read_delim.html'>read_csv</a></span><span class='op'>(</span><span class='st'>'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-01-18/chocolate.csv'</span><span class='op'>)</span>

<span class='fu'>skimr</span><span class='fu'>::</span><span class='fu'><a href='https://docs.ropensci.org/skimr/reference/skim.html'>skim</a></span><span class='op'>(</span><span class='va'>chocolate</span><span class='op'>)</span>
</code></pre></div>

Table: (\#tab:unnamed-chunk-2)Data summary

|                         |          |
|:------------------------|:---------|
|Name                     |chocolate |
|Number of rows           |2530      |
|Number of columns        |10        |
|_______________________  |          |
|Column type frequency:   |          |
|character                |7         |
|numeric                  |3         |
|________________________ |          |
|Group variables          |None      |


**Variable type: character**

|skim_variable                    | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:--------------------------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|company_manufacturer             |         0|          1.00|   2|  39|     0|      580|          0|
|company_location                 |         0|          1.00|   4|  21|     0|       67|          0|
|country_of_bean_origin           |         0|          1.00|   4|  21|     0|       62|          0|
|specific_bean_origin_or_bar_name |         0|          1.00|   3|  51|     0|     1605|          0|
|cocoa_percent                    |         0|          1.00|   3|   6|     0|       46|          0|
|ingredients                      |        87|          0.97|   4|  14|     0|       21|          0|
|most_memorable_characteristics   |         0|          1.00|   3|  37|     0|     2487|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|    mean|     sd|   p0|  p25|     p50|    p75| p100|hist                                     |
|:-------------|---------:|-------------:|-------:|------:|----:|----:|-------:|------:|----:|:----------------------------------------|
|ref           |         0|             1| 1429.80| 757.65|    5|  802| 1454.00| 2079.0| 2712|▆▇▇▇▇ |
|review_date   |         0|             1| 2014.37|   3.97| 2006| 2012| 2015.00| 2018.0| 2021|▃▅▇▆▅ |
|rating        |         0|             1|    3.20|   0.45|    1|    3|    3.25|    3.5|    4|▁▁▅▇▇ |

</div>


# separate_rows()

As you may have noticed, the most memorable characteristics of a chocolate are organized in a string with multiple characteristics separated by commas.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>chocolate</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://dplyr.tidyverse.org/reference/sample_n.html'>sample_n</a></span><span class='op'>(</span><span class='fl'>10</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://dplyr.tidyverse.org/reference/pull.html'>pull</a></span><span class='op'>(</span><span class='va'>most_memorable_characteristics</span><span class='op'>)</span> 
</code></pre></div>

```
 [1] "dominate cocoa notes"          "muted, nutty"                 
 [3] "intense, sweet, brownie"       "creamy, complex, coffee"      
 [5] "sandy, tobacco, molasses"      "strawberry, butterscotch, off"
 [7] "nutty, off, roasty, fatty"     "astringent, smomkey cocoa"    
 [9] "grassy, earthy, coffee"        "gritty, sticky, smoke,hammy"  
```

</div>


Often, we would like to "unlist" those characteristics, maybe to understand how people describe chocolate. One way of doing this is using 'separate()'. However, this poses an inconvenience when the number of objects in the list is unknown or variable. 'separate_rows()' simplifies the process.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>chocolate_separated</span> <span class='op'>&lt;-</span> <span class='va'>chocolate</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> 
    <span class='fu'><a href='https://dplyr.tidyverse.org/reference/select.html'>select</a></span><span class='op'>(</span><span class='va'>most_memorable_characteristics</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> 
    <span class='fu'><a href='https://tidyr.tidyverse.org/reference/separate_rows.html'>separate_rows</a></span><span class='op'>(</span><span class='va'>most_memorable_characteristics</span>, sep <span class='op'>=</span> <span class='st'>", "</span><span class='op'>)</span> 
</code></pre></div>

</div>


This makes counting easy.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>chocolate_separated</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> 
    <span class='fu'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='op'>(</span><span class='va'>most_memorable_characteristics</span>, sort <span class='op'>=</span> <span class='cn'>T</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> 
    <span class='fu'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='op'>(</span><span class='fl'>10</span><span class='op'>)</span>
</code></pre></div>

```
# A tibble: 10 x 2
   most_memorable_characteristics     n
   <chr>                          <int>
 1 sweet                            264
 2 nutty                            259
 3 cocoa                            242
 4 roasty                           209
 5 earthy                           187
 6 creamy                           186
 7 sandy                            164
 8 fatty                            163
 9 floral                           143
10 spicy                            138
```

</div>


```{.r .distill-force-highlighting-css}
```
