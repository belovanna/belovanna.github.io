---
layout: single
title:  "Web scraping"
excerpt: "Web scraping: an example of data extraction with lxml library."
date:   2018-03-06 11:10:00 +0300
toc: TRUE
toc_label: "On this page"
toc_icon: "cog"
tags:
  - data analysis
  - tutorial
categories: python
header:
  image: /assets/images/banner.jpg
  teaser: /assets/images/scraping.jpg
---
### What is web scraping

Websites do not always provide their data in nice formats like JSON or csv.
With economic data, it often happens that you need a dataset which only presented as an
HTML table on a webpage and isn't available for downloading. In such cases, the remedy can
come from *web scraping*.

> Web scraping is a technique of extracting website information; it helps to get your hands
on the data from web pages.

Python has libraries like *BeautifulSoup* and  *lxml* useful for web scraping.

* *lxml* is a library for parsing XML and HTML. It's fast, straightforward and intuitive to use.
It's generally suggested to use *lxml* solution with well formed sources.

* *BeautifulSoup* is designed for extracting data out of poorly-formed HTML or XML. It has
great detailed documentation. *BeautifulSoup4* now supports using lxml as the internal parser,
so the choice between the two libraries is really the matter of preferences.

### Example: Bloomberg Billionaires Index

> The Bloomberg Billionaires Index is a daily ranking of the world’s richest people.
Details about the calculations are provided in the net worth analysis on each billionaire’s profile page.
The figures are updated at the close of every trading day in New York

Let's try to get some data from the [Bloomberg Billionaires Index](https://www.bloomberg.com/billionaires/ "bloomberg page")
dataset by exploiting *lxml* and *Request* (an HTTP library).

```py
pip install lxml
pip install requests
```
Next we'll use `request.get()` to get the webpage with our dataset, parse it with `html.fromstring()` and
save the result in `tree`.  

```py
page = requests.get('https://www.bloomberg.com/billionaires/')
tree = html.fromstring(page.content)
```
Now we'll leverage `xpath` for addressing parts of an XML document. A tutorial is [here](https://www.w3schools.com/xml/xpath_nodes.asp).
There're various ways to inspect webpage elements. I'm using safari, so I go with Develop -> Show Web Inspector.
A quick glance at the structure of our page, reveals that a row of our dataset looks like this:

``` html
<div class="table-row">
					<div class="table-cell t-rank">1</div>
					<div class="table-cell t-name"><a href="./profiles/jeffrey-p-bezos/">Jeff Bezos</a></div>
					<div class="table-cell active t-nw">$127B</div>
					<div class="table-cell t-lcd pos">+$1.87B</div>
					<!-- <div class="table-cell t-lcp pos">+1.5%</div> -->
					<div class="table-cell t-ycd pos">+$27.9B</div>
					<!-- <div class="table-cell t-ycp pos">+28.2%</div> -->
					<div class="table-cell t-country">United States</div>
					<div class="table-cell t-industry">Technology</div>
				</div>
```
The data is contained in the div elements. Knowing this structure, let's create the queries
for extracting Rank, Name, and Total net worth:

```py
#This will extract ranks
rank = tree.xpath('//div[@class="table-cell t-rank"]/text()')
#This will extract billionair's names
name = tree.xpath('//div[@class="table-cell t-name"]/a/text()')
#This will extract total net worth
activetnw = tree.xpath('//div[@class="table-cell active t-nw"]/text()')
```
Let's see what this yields by printing the output:

```py
#print ('rank: ', rank)
#print ('name: ', name)
#print ('activetnw: ', activetnw)
```
```py
rank:  ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', '13', '14', '15', '16', '17', '18', '19', '20',...]
name:  ['Jeff Bezos', 'Bill Gates', 'Warren Buffett', 'Mark Zuckerberg', 'Amancio Ortega', 'Carlos Slim', 'Bernard Arnault', 'Larry Ellison', 'Larry Page', 'Sergey Brin',...]
activetnw:  ['$127B', '$91.4B', '$87.1B', '$73.9B', '$66.0B', '$65.7B', '$64.6B', '$56.9B', '$54.0B', '$52.7B', '$47.9B', '$47.9B', '$46.9B', '$43.4B', '$43.4B', '$42.9B', ...]
```
Yes! We get three lists containing our data. The web scraping was successful. Now, we'd
like to retrieve the rest of the information in the table, and save our data to a csv file.

### Saving the retrieved data

We shall start with importing pandas and writing our lists into a dataframe. The following code

``` py
import pandas
df = pandas.DataFrame(data={"rank": rank, "name": name, "activetnw": activetnw})
df.to_csv("./myfile.csv", sep=',',index=False)
```
however, produces an error: `ValueError: arrays must all be same length`. Checking the length of
the lists, returns:

```py
print(len(rank), len(result), len(activetnw)) #check the length of the str

500 499 500
```
It's clear, we're dealing with some missing values here. After a closer inspection, it turns out that
the problem is caused by the anonymous entry #466:

![My helpful screenshot]({{ "/assets/images/webscraping/empty.png" }})

Our parsing is ignoring empty entries in name, country and industry classes. This will mess up the
dataset later on, unless we fix it.

```py
	<div class="table-row">
					<div class="table-cell t-rank">466</div>
					<div class="table-cell t-name"><a href="./profiles//"></a></div>
					<div class="table-cell active t-nw">$4.30B</div>
					<div class="table-cell t-lcd pos">+$163M</div>
					<!-- <div class="table-cell t-lcp pos">+3.9%</div> -->
					<div class="table-cell t-ycd pos">+$1.48B</div>
					<!-- <div class="table-cell t-ycp pos">+52.8%</div> -->
					<div class="table-cell t-country"></div>
					<div class="table-cell t-industry"></div>
				</div>
```
However, the fix is really easy. We can modify our parsing code as follows:

```py
name = tree.xpath('//div[@class="table-cell t-name"]/a')
name_res = [x.text if x.text else '' for x in name]
```
Now the lengths of lists are `500 500 500` and we can proceed with pandas dataframe,
and writing a csv. The remaining columns of the table are extracted using similar approach.
The full script and resulting file are on my [github](https://github.com/belovanna/py_dir).
