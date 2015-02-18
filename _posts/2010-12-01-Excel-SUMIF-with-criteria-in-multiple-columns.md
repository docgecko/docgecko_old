---
layout: article
title: "Excel SUMIF with criteria in multiple columns"
categories: articles
image:
  teaser: sumif.jpg
---

The SUMIF function is designed to SUM one column if another column contains the some expected criteria.  For those who have already noticed, the SUMIF function does not work when your criteria exists in multiple columns.  This is an issue I just had to face.

## Multiple Column Criteria

When criteria exists in more than 1 column, you have to take a completely different approach, e.g. when you have a situations such as given in the table below:

<img src="/public/images/excel-sumif.png" alt="Excel SUMIF" width="230px" border="0"/>

Here I have 3 columns:

* *Column A* contains a *Length* (in meters).
* *Column B* contains a *Code* (i.e. 2, 4a & 4b)
* *Column C* contains a *Score* (1 to 4)

I need to check that both data in Columns B and C match the criteria of specific Code and Score, and for those that match both criteria, I need to SUM the lenths in Column A.

To do this you need to use an *Array Formula*, also known as a *CSE Formula* or even a *Super Formula*.  This is a formula that requires you to press *Ctrl+Shift+Enter* after typing it, instead of just Enter.

## Example

If you wanted to sum together all Lengths where the Code is 4a and the Score is 2, here is the formula you can to use:

<pre>=SUM((B2:B11="4a")*(C2:C11="2")*A2:A11)</pre>

This will find that rows 4 and 11 contain both criteria at the same time, and will thus return the sum of lengths in those columns, i.e. A4 and A11, which is 141.09m + 731.38m = 872.47m.

The formula looks a little strange and probably not what you'd expect; however, it works very well, as long as you remember to use *Ctrl+Shift+Enter* instead of Enter when inputting the formula.
