---
layout: post
title: Week 1 at Metis - Learning to Stay Afloat
---

"Imagine you are in a pool. You should feel that the water level is just about at neck level".
This metaphor was introduced on the first day to describe how the workload should feel while at Metis.

This wasn't surprising. After all, this is a bootcamp; I know what I signed up for.

Over the past three years, I had been working as an analyst for a small healthcare consulting firm. To stick with the same pool metaphor, I frequently felt the water level wasn't even at knee level, so it's refreshing to be learning how to stay afloat again. 

The first week at Metis had a focus on exploratory data analysis using [pandas] (https://pandas.pydata.org/). The goal was to use the New York MTA turnstile data to provide the optimal subway locations to concurrently build awareness and reach for a fictional annual summer gala being held by WomenTechWomenYes (WTWY). The information in the MTA data set included the cumulative number of entries per four hour blocks per turnstile. 

Of course, everyones first thought was to find the subway locations with the highest foot traffic by time. Not surprisingly, the time slots with the highest foot traffic encompassed the morning and evening rush hours, and we decided to focus our analysis on these times.

The more interesting portion of this project (to me at least), was the additional data sources that we would use to augment the turnstile data to identify riders that have a higher potential to contribute to the summer gala. We decided to use the [American Community Survey] (https://www.census.gov/programs-surveys/acs/) to target driven, professional women, and used locations with high concentration of tech business centers to target those who worked in the tech industry.

We immediately weighted our turnstile with our auxiliary datasets and began developing our recommendations. It wasn't until we had already begun creating our PowerPoint that we decided to take a step back and take a look at things from a higher level. We had overlooked a crucial issue. Because we are looking at entries into the turnstile, we are likely capturing riders coming from work during the evening commute, and capturing riders coming from home during the morning commute. 

We were drowning in the details and had lost our focus. Luckily, it wasn't too much of problem to re-segment our data so that we only used the demographics to weight our morning commute entries, and only use the tech business center data to weight our evening commute entries. It definitely served as a good reminder that we should often take a step back to look at things from a different perspective.

The first week at Metis is over, and although I'm treading water, I'm slowly getting more comfortable in the pool. 





