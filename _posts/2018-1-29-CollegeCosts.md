---
layout: post
title: What is Driving College Costs Higher?
---

Over the past 30 years, college costs have been rising exponentially, and the increase has been especially clear when
compared against the increase in income over the same period of time. So I decided to see if I could analyze whether there
were certain attributes of these colleges that were driving the cost of tuition.

I decided to get data from [CollegeData.com](https://www.collegedata.com/), which is a website that mainly acts 
as an online college advisor for prospective students. For each college page, they have information on admissions, financial aid, academics, campus life etc.

## Data Scraping - Using Selenium and Scrapy ##

The next step was figuring out how to get the data. I had previously worked with BeautifulSoup and lxml to scrape data from
the web, but the websites that I scraped had been fairly straightforward.

A quick look at the CollegeData website and I could tell this wasn't going to be as simple. Here were a couple of challenges that I needed to overcome:

1. There is no full list of colleges that are listed on the site
2. Each individual college page had a unique ID in the URL, but they are not in order. For example, one college could have an ID of 436, but there would be no colleges with an ID from 400-435. There was no pattern in how they selected ID's.
3. A search query cannot result in more than 500 colleges, as this exceeds their maximum query, and will return nothing.

I decided to delve deeper into using Selenium and Scrapy to try and tackle this problem. I used Selenium to generate multiple search queries (that resulted in less than 500 colleges) to gather all the links to individual college pages, and then use Scrapy to collect data from those pages. Another reason to use Selenium for the search query portion of this exercise was that each search query would return 15+ pages of 25 colleges, but each page would not have a distinct url since the search results were within a JHTML object. As a result, the best way to navigate the pages would be clicking the page numbers.

After investigating the different parameters that could be used in a search query (such as student body size, cost, city, state etc.) I decided that the method that would use the least amount of queries would be using their region selector. I could generate 5 separate queries that would capture all colleges on the site.

![Region Selector]({{ site.baseurl }}/images/regions-colleges.png "Region Selector on CollegeData")

A region could be selected using the click of a mouse, which can be achieved with a code snippet like below, where (region) is one of the potential regions on the map:

```python
element = driver.find_element_by_xpath('//*[@id="%s"]' % (region))                   
element.click()
```



