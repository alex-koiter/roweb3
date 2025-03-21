---
title: CRediTas is Now Part of rOpenSci
author: "Josep Pueyo-Ros"
date: "2023-05-16"
slug: "creditas-is-now-part"
tags:
- tech notes
- R
- contributors
- scientific publications
- Software Peer Review
- CRediTas
- community
package_version: 0.2.0
description: CRediTas is an R package for generating CrediT author statements for scientific publications.
tweet: CRediTas is Now Part of rOpenSci by @jospueyo
params:
  doi: "10.59350/eyxmp-3md27"
---

I'm thrilled to share that [CRediTas](https://docs.ropensci.org/CRediTas/) has passed [peer review](https://github.com/ropensci/software-review/issues/576) and been accepted to rOpenSci as well as to [CRAN](https://cran.r-project.org/web/packages/CRediTas/index.html). I am glad to acknowledge the editor [Emily Riedered](/author/emily-riederer/) and the two reviewers [Marcelo S. Perlin](/author/marcelo-s.-perlin/) and [João Martins](https://zambujo.github.io/). Their comments and support were really insightful.

CRediTas is a tiny package to facilitate the tedious job of creating [CRediT authors statements](https://credit.niso.org/) for scientific publications. Normally, the first author of a scientific paper organizes a table in a spreadsheet where all the authors self-state their contributions. Often too, it is the first author's responsibility to state the contributions of all co-authors. However, at the end, the information has to be translated to the CRediT statement format of “Author 1: roles Authors 2: roles …” which is prone to errors and tedious, especially if there are many co-authors. The CRediTas package aims to make this easier by providing a template to be filled in form of a table (csv) and then converting this table to CRediT statement format.

The workflow is meant to work with three basic functions. First, we create a template table. It can be created as a `data.frame` and being populated in R. Or as a csv file and being populated in your preferred csv editor.


```r
library(CRediTas)

# Save the template to a csv file
template_create(authors = c("Alexander Humboldt", "Carl Ritter"), file = tempfile())

# Save the template as an R data.frame
cras_table <- template_create(authors = c("Friedrich Ratzel", 
                                          "Pau Vidal de la Blache", 
                                          "Pau Vila",
                                          "Élisée Reclus"))
```

The template contributions are empty. So you must provide the information of who did what. You can use the `fix()` function to open and fill the template directly in R:


```r
fix(cras_table)
```

Or if you wrote the template to a file, once populated with the contributions, you can read it back to R as follows:


```r
cras_table <- template_read(path_to_your_csv_file)
```

Once the `cras_table` is populated, for instance:


|Authors                | Conceptualization| Methodology| Software| Validation| Formal Analysis| Investigation| Resources| Data curation| Writing - original draft| Writing - review & editing| Visualization| Supervision| Project administration| Funding acquisition|
|:----------------------|-----------------:|-----------:|--------:|----------:|---------------:|-------------:|---------:|-------------:|------------------------:|--------------------------:|-------------:|-----------:|----------------------:|-------------------:|
|Friedrich Ratzel       |                 0|           0|        0|          0|               1|             0|         1|             0|                        1|                          0|             1|           0|                      0|                   1|
|Pau Vidal de la Blache |                 0|           0|        0|          0|               1|             0|         1|             0|                        1|                          0|             0|           1|                      0|                   0|
|Pau Vila               |                 0|           0|        0|          0|               0|             0|         0|             0|                        0|                          0|             0|           0|                      0|                   0|
|Élisée Reclus          |                 1|           1|        0|          1|               0|             0|         0|             0|                        0|                          0|             1|           1|                      0|                   0|

Then, a text file can be generated following the CRediT author statement format:


```r
cras_write(cras_table, "textfile.txt", markdown = TRUE, quiet = TRUE)
```

If you open the text file, you will find this statement, all ready to go!

**Friedrich Ratzel:** Formal Analysis, Resources, Writing - original draft, Visualization, Funding acquisition **Pau Vidal de la Blache:** Formal Analysis, Resources, Writing - original draft, Supervision **Élisée Reclus:** Conceptualization, Methodology, Validation, Visualization, Supervision

You can also print the statement directly in a Rmarkdown file using an inline chunk: `` `r
cras_write(cras_table, markdown = TRUE)` ``.

More information on [the package vignette](https://docs.ropensci.org/CRediTas/articles/get_started.html).

Hopefully, this simplifies your life as it simplified mine and allows you to spend more time on actually writing your articles. Also, to better manage your co-authors contributions. Please report any issues you have!
