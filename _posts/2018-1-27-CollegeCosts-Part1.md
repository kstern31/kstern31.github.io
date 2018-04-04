---
layout: post
title: What is Driving College Costs Higher? Part 1 - Data Collection and Cleaning
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

```
element = driver.find_element_by_xpath('//*[@id="%s"]' % (region))                   
element.click()
```

For each of these search queries, I could then select URLs for each college, and store them in a list. Finally I threw this data into a pickle file, so that I could access them using my scrapy spider. The code for my Selenium script and Scrapy spider can be found on my [github](https://github.com/kstern31/Whats-Driving-College-Costs-Higher).

## Data Cleaning ## 

Since the purpose of this analysis was to analyze what attributes had the largest effect on the cost of tuition, the first data cleaning step was to drop any colleges where the dependent variable was missing. For independent variables, if that column had more than 40% missing, I would drop that variable, otherwise I would fill the column with the mean of that variable, stratified by institution type (public vs. private). Although this isn't the perfect solution to handling missing data, I decided this was sufficient considering the timeline of this project (2 weeks).


Some colleges had multiple costs listed (in state vs. out of state), and I decided to use the out of state across the board, as I felt this cost is more indicative of the rising college costs. The cost variables were in list format if there were in state and out of state costs, so for any cost variables with a list of length 2, replace it with the second value (which is the out-of-state cost.

```python
df['totalCost'][df.totalCost.str.len() == 2] = pd.DataFrame(df.loc[df.totalCost.str.len() == 2].totalCost.values.tolist(), index= df.loc[df.totalCost.str.len() == 2].index)[1]
```

Below are some example of data cleaning I did for the independent variables. This served as a good exercise in getting more comfortable with pandas, lambda functions, and data exploration in general. 

The class size variable was brought in as a list of bins with the percent that each bin is represented. An example is shown below:

```
2-9 students: 23% of classes
10-19 students: 25% of classes
20-29 students: 25% of classes
30-39 students: 14% of classes
40-49 students: 7% of classes
50-99 students: 5% of classes
Over 100 students: 0% of classes
```

I wanted to get a single number that I could use for my linear regression model, so I took the median of each bin, multiplied it by the percent that its represented, and then summing all of these values to calculate the average class size for each college.
```python
classSizedf = pd.DataFrame(df.classSize.values.tolist(), index= df.index)
#replace Nones and "Not reported" with NaN
classSizedf = classSizedf.replace("Not reported", np.nan)
classSizedf.fillna(value=np.nan, inplace = True)
#cleaning white space at beginning and end of lists, and splitting into elements
classSizedf = classSizedf.apply(lambda x: x.str.strip())
classSizedf = classSizedf.apply(lambda x: x.str.split(' '))

#for most of the lists, the first element will be the bin (e.g. 2-9 students), and the third element will be the %.
#these lists have a length of 5. The other lists be in the format [Over 100 students: 0% of classes] and have a 
#length of 6, so we can just get the second element and multiply it by the 4th.
def getMedian(l):
    if len(l) == 6:
        return float(l[1].strip()) * float(l[3].strip('%'))/100
    else:
        return sum(map(float, l[0].split('-')))/2 * float(l[2].strip('%'))/100

classSizeBins = classSizedf.applymap(lambda x: x if type(pd.isnull(x)) == bool else getMedian(x))
classSizeBins['avgClassSize'] = (classSizeBins[0].fillna(0) + classSizeBins[1].fillna(0) + 
                                 classSizeBins[2].fillna(0) + classSizeBins[3].fillna(0) +
                                 classSizeBins[4].fillna(0) + classSizeBins[5].fillna(0) +
                                 classSizeBins[6].fillna(0))

df['avgClassSize'] = classSizeBins['avgClassSize']
df.avgClassSize.replace(0, np.nan, inplace = True)
```

Most of the colleges had single values for ACT/SAT scores, but there were some where there was a range for the scores. For these values, I wanted to take the average of the range, and I detected this by the use of a "-" in the score.

```python
#for values with a range, take the average, else just return the value
def getScore(s):
    s = s.split(' ')[0]
    if '-' in s:
        return sum(map(float, s.split('-')))/2
    else:
        return float(s)

df.ACT = df.ACT.apply(lambda x: x if pd.isnull(x) else getScore(x))
df.SATMath = df.SATMath.apply(lambda x: x if pd.isnull(x) else getScore(x))
df.SATReading = df.SATReading.apply(lambda x: x if pd.isnull(x) else getScore(x))
```
After further exploration of the ACT/SAT scores, there seemed to be some extreme outliers (e.g. 140 SAT Math score for UPenn). After checking the actual URL, I found that there were incorrect answers on the website itself, so I manually imputed the values for a couple of colleges in my data set. I'm sure I didn't get all the incorrect values, but I had to settle for only correcting the extreme outliers in the interest of time.

The rest of my data exploration and cleaning can be found on my [github](https://github.com/kstern31/Whats-Driving-College-Costs-Higher/tree/master/dataExploration).

Thanks for reading part 1! The next part of the process will be about the data analysis and will be up soon.

