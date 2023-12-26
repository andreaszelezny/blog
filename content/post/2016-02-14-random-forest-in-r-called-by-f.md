---
title: 'Random Forest in R Called by F#'
author: Andy
type: post
date: 2016-02-14T22:55:47+00:00
url: /post/random-forest-in-r-called-by-f/
showtoc: false
categories:
  - Machine Learning
tags:
  - 'f#'
  - r
  - random forest

---
Random forest analysis was performed to evaluate the importance of a series of explanatory variables in predicting a binary, categorical response variable. The following explanatory variables were included as possible contributors to a random forest evaluating after surgery deformation (kyphosis) (my response variable), age in months (Age), number of vertebrae involved (Number), and the highest vertebrae operated on (Start).

The accuracy of the random forest was 80%, with the subsequent growing of multiple trees rather than a single tree, adding little to the overall accuracy of the model, and suggesting that interpretation of a single decision tree may be appropriate.

Source code in F#:

<pre>#I "../packages/RProvider.1.1.15"
#load @"..\packages\RProvider.1.1.15\RProvider.fsx"

open RDotNet
open RProvider
open RProvider.rpart
open RProvider.randomForest

let fit = namedParams [ "x", box kyphoX; "y", box kyphoY ] |> R.randomForest
R.print fit
</pre>

Which created the following output:

<pre>Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 1

        OOB estimate of  error rate: 19.75%
Confusion matrix:
        absent present class.error
absent      60       4   0.0625000
present     12       5   0.7058824
</pre>