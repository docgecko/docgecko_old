---
layout: article
title: "Excel cell validation - counting of non-blank cells"
categories: models
comments: true
ads: true
image:
  teaser: data-validation.jpg
---

When building Excel-based models, more-and-more it is becoming clear that it is necessary to include such functionality as Validation and, to some degree, to the degree expected from web-based software.

With a model I am building at the moment, it has been necessary to reduce the chance of a user not including certain data that would consequently cause the model to crash when run.  As such, to reduce the likelihood of not entering all of the required data, I am building in checks that will then provide an error list if certain data is not entered.

## Error Checking

The error checking includes a count of remaining errors in an obvious place on the input excel sheet, i.e. in a place that can be easily checked by the user.

So my problem was that I need to firstly check each input to see if data has been entered or not.  So firstly I have a single column of cells (e.g. column A3:A()) that must be filled with data by the user.  In the column next to this (e.g. column B3:B8) is the data check.  This column contains an initial data check, i.e. Cell B3 contains the following (which can be copied down the other cells in column B):

{% highlight text %}
=IF(AND($A3<>""),"Please enter data", "")
{% endhighlight %}

So if the user fills in cell A3, B3 remains blank, i.e. it does not report an error.  However, if A3 does not contain data, the error appears in Cell B3 saying "Please enter data"

## Error Count

So my actual problem was now to count the number of remaining errors, i.e. how to count the number of cells in column B that report the error.  Sounds easy to do right...well, thats what I thought.  I thought I have to do is count the number of NON BLANK cells in column B.  Well, for example using any of the following do not work:

{% highlight text %}
=COUNTIF(B3:B8,<>"") or =COUNTIF(B3:B8,"<>""") or =COUNTIF(B3:B8,"<>")
{% endhighlight %}

In the end I used an approach offered by [Prasanth Chandra](http://pcsplace.com/tips-n-tricks/how-to-count-blank-and-non-blank-cells-in-ms-excel/).  What you have to do is essentially count all of the cells in the range and remove from that the number of blank cells, as follows:

{% highlight text %}
=(ROWS(B3:B8)*COLUMNS(B3:B8))-COUNTBLANK(B3:B8)
{% endhighlight %}

Looks complex, but works right away!
