---
layout: single
categories: posts
tags: posts
title: Graphing NSW COVID Cases with Python
---

## Rationale

I currently have uni holidays in lockdown and looking at the cases rising exponentially every single day is really demotivating and frustrating. However, looking at the numbers every passing day made me curious and I wanted to estimate the growth of cases in the state using basic models.

## Goals

I had a few goals in mind when I started researching:

- Find COVID data online and a way to fetch that data using Python
- Plot the data on a curve
- Find the equation to a curve of best fit
- Approximate the number of subsequent cases

## Research and Links

Looking through different websites and trying to find different sources of data, I found a page with [dataset with state COVID cases](https://data.nsw.gov.au/search/dataset/ds-nsw-ckan-97ea2424-abaf-4f3e-a9f2-b5c883f42b6a/distribution/dist-nsw-ckan-2776dbb8-f807-4fb2-b1ed-184a6fc2c8aa/details?q=) on a *data.nsw.gov.au* subpage.

There were also a few other pages with interesting data. Although I didn't use them in this mini-project, I might revisit this after lockdown is over and add more data like tests and vaccination rates to my graph.
Other interesting data: [COVID-19 data with graphs](https://www.covid19data.com.au/nswa), [Our World in Data](https://ourworldindata.org/covid-vaccinations?country=AUS), [Peclet Technology datasets](https://data.peclet.com.au/explore/?disjunctive.features&disjunctive.modified&disjunctive.publisher&disjunctive.keyword&disjunctive.theme&disjunctive.language&sort=modified).

## Getting the data using Python
In order to experiement with the data provided, I created a quick program to retrieve information from the first *n* COVID cases in NSW.
~~~python
import urllib.request
import json

# get cases as json
# you can change limit number to increase the limit of cases searched for
limit_number = 1
url = f'https://data.nsw.gov.au/data/api/3/action/datastore_search?resource_id=2776dbb8-f807-4fb2-b1ed-184a6fc2c8aa&limit={limit_number}'
fileobj = urllib.request.urlopen(url)

# y stores the json for the cases
y = json.load(fileobj)
~~~

When I printed out the cases, it returned a lot of messy text.

~~~
{'help': 'https://data.nsw.gov.au/data/api/3/action/help_show?name=datastore_search', 'success': True, 'result': {'include_total': True, 'limit': 1, 'records_format': 'objects', 'resource_id': '2776dbb8-f807-4fb2-b1ed-184a6fc2c8aa', 'total_estimation_threshold': None, 'records': [{'notification_date': '2020-10-10', 'postcode': None, 'likely_source_of_infection': 'Overseas', 'lhd_2010_code': None, 'lhd_2010_name': None, 'lga_code19': None, 'lga_name19': None}], 'fields': [{'id': 'notification_date', 'type': 'text'}, {'id': 'postcode', 'type': 'text'}, {'id': 'likely_source_of_infection', 'type': 'text'}, {'id': 'lhd_2010_code', 'type': 'text'}, {'id': 'lhd_2010_name', 'type': 'text'}, {'id': 'lga_code19', 'type': 'text'}, {'id': 'lga_name19', 'type': 'text'}], '_links': {'start': '/api/3/action/datastore_search?resource_id=2776dbb8-f807-4fb2-b1ed-184a6fc2c8aa&limit=1', 'next': '/api/3/action/datastore_search?resource_id=2776dbb8-f807-4fb2-b1ed-184a6fc2c8aa&limit=1&offset=1'}, 'total': 30618, 'total_was_estimated': False}}
~~~

However, looking through this, I realised what pieces of information mattered. The next step was to tally all the cases per day. I created a dictionary with the current date as the key and case number as the value. Cycling through each case's data also allows me to come back later and sort by other criteria such as location or age which is something I want to look at in the future.

~~~python
# loop through the cases and find how many in 2021 because we
# do not want to look at 2020
for item in y['result']['records']:
    if item['notification_date'][0:4] == '2021':
        if int(item['notification_date'][5:7]) >= 7:
            # print(item['notification_date'])
            d[item['notification_date']] += 1;

# create lists with sorted dates and corresponding case numbers
date_list = sorted(list(d.keys()))
case_list = []

for date in date_list:
    case_list.append(d[date])
~~~

When I printed out the list with cases, it produced the following.

~~~
[31, 42, 24, 31, 17, 34, 37, 46, 52, 107, 121, 73, 91, 76, 102, 114, 99, 111, 66, 107, 143, 144, 150, 141, 176, 143, 158, 254, 182, 197, 242, 194, 208, 237, 247, 320, 302, 252, 260, 367, 339, 372, 401, 454, 325, 508, 580, 550, 640, 764, 771, 818, 745, 832, 918, 968, 953, 1141, 1144, 1229, 1104, 1249, 1380, 1067]
~~~

This was exactly what I wanted because it meant that I had a whole list with daily COVID cases which I could plot on a curve and analyse.

*Minor consideration and point for more research:* for some reason, the cases for the previous day are always significantly lower than they should be and this only gets updated the subsequent day. Removing this ensures a smoother curve.

## Plotting the Curve

To plot the curve, I used *numpy* to convert the data into an array and *matplotlib* to actually plot the curve.

~~~python
# generate arrays for dates and corresponding cases
x = np.array(list(range(len(date_list))))
y = np.array(case_list)

# plot graph with blue line
plt.figure()
plt.plot(x, y, 'b')
plt.show()
~~~

And this produces the following curve plotting case numbers against days since 1st July 2021.

![](/assets/images/Post-Graphing_COVID/covid_curve.png)

## Modelling the curve

The first step to modelling the curve was finding a function that could work out an equation based on the data points we found. After considering various options, I decided to use scipy's curve_fit function.

### Linear

I initially used a simple linear curve \\(y = mx + b\\).

~~~python
[a, b], _ = scipy.optimize.curve_fit(lambda t, a, b: a + b*x,  x,  y)
y1 = a + b*(x)
plt.plot(x, y1, 'r')
~~~

Even though this didn't produce anything like the COVID graph, comparing it to other models shows how they improve.

![](/assets/images/Post-Graphing_COVID/covid_linear.png)

### Quadratic

The next simplest option was a quadratic curve \\(y = ax^2 + bx + c\\).

![](/assets/images/Post-Graphing_COVID/covid_quadratic.png)

### Cubic

Some of the problems I noticed with the quadratic graphs were:

1. The vertex of the parabola didn't start at day 0 and so the parabola interpolated with a high degree of error around \\(0 \le t \le 32\\).
2. As COVID gets worse, the cases rise at a faster rate. The parabola is barely keeping up with the graph right now and in the future, a degree 2 polynomial simply will not be able to match the rate of change of cases.

These shortcomings indicated that it was probably a good idea to try something other than a polynomial because that wasn't suited for the graph. Nonetheless, I decided to try a cubic graph because I was curious whether a graph with 2 turning points would solve the first problem I identified.

![](/assets/images/Post-Graphing_COVID/covid_cubic.png)

Looking at this graph, the cubic much more closely matched the COVID cases. The first shortcoming of the quadratic graph wasn't apparent in this cubic graph and I'm pretty sure using any polynomial with degree greater than 2 will ensure that remains the case.

### Septic

*I didn't know a degree 7 polynomial was called a septic...* I was curious to try one more polynomial with a significantly higher degree so I tried a degree 7 polynomial.

![](/assets/images/Post-Graphing_COVID/covid_seven.png)

Although this curve fit very well, it had the same problem as all the other polynomial curves and couldn't keep up with the rate of change of the COVID-cases graph.

### Exponential Curve

The curve fit function I was using needed me to plot some transformation of y against that of x. So, I used $$ log(y) = ax + b $$ to give me an exponential curve in the form of $$ y = Ae^{kx} $$.

~~~python
[a, b], _ = scipy.optimize.curve_fit(lambda t, a, b: a*x + b,  x, np.log(y))
y1 = np.exp(a*x + b)
plt.plot(x, y1, 'r')
~~~

![](/assets/images/Post-Graphing_COVID/covid_lin_exp.png)

This was the best approximation for the COVID graph I could find using a simple equation to model the curve. Looking at the end of the graph, it was evident that this was much better than the polynomial curves because this curve's gradient was about the same as the COVID curve itself.

## Predicting Future COVID Values

Using the exponential curve, I created a function $ g $ to predict the number of cases a specified number of days after 1st July 2021.

~~~python
def g(t):
	return np.exp(a*t + b)
~~~

Using this, I interpolated some values given by the graph. *All predicted values are rounded to nearest person.*

| Date        | Predicted | Actual |
| ----------- | --------- | ------ |
| 2021-08-15  | 514       | 508    |
| 2021-08-20  | 695       | 771    |
| 2021-08-25  | 941       | 968    |
| 2021-08-30  | 1275      | 1104   |
| 2021-09-04  | 1726      | ???    |

## Limitations

The biggest limitation of the model I used is that the curve will continue to exponentially rise. In reality, the number of cases will grow at an increasing rate to a certain point and then grow at a decreasing rate and level off. This is referred to as logistic growth and would probably be a better model in countries with high COVID densities. It isn't necessary in NSW because the case numbers are not nearly at the inflection point of a logistic curve but if the numbers continue rising, the rate of change will start decreasing eventually.

## Next Steps

I want to model percentage of the population vaccinated on the same graph and look at how the increase in vaccinations is affecting the curve. Additionally, I want to look at different COVID restrictions implemented over the timeline of the graph and work out whether they lowered the approximated exponential graph before the restrictions took effect.