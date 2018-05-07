---
layout: post
title: Creating a housing price prediction model using data from Zillow.com
published: true
---
![zillow](https://raw.githubusercontent.com/10brink/10brink.github.io/master/Zillow-logo.jpg)

I wanted to see if I could predict housing values in my hometown of Ann Arbor MI, using data from Zillow.com. Though zillow already has a feature like that called Zestimate, I wanted to make my own. I wanted to create a model that used different atributes of a house (square footage, number of bedrooms and bathrooms, etc.) to predict the sale price. This could also be used to see how much your house value goes up if you build an addition or install another bathroom in your house.  


Zillow has a nice api, however, to make a call (ie, get info from it) you must have the exact address of the listing. There is no way to search the api as you can on the website. Since my 'target' (what I'm trying to predict) is sale prices, I searched the website for recently sold houses, using selenium and beautiful soup to scrape the addresses of 1000 recently sold houses in Ann Arbor. 

Now that I had a list of addresses, I could plug those into the api to get the data that I needed for my model. 

After removing data with missing values I ended up with about 400 homes in my dataset. 

|   Feature    | Min| Max  | Mean   | Std Dev|
| ------|:------:|:-------:| :-----:| :-----: |
| House Size in Sq. Feet | 560 | 10,969 | 1,876 | 1,008|
| Lot Size (sq ft)     | 871    |   3,484,800 | 40,900 | 187,434 |
| Year Built | 1885  |  2018 | 1964 | 26.5 |
|  Bedrooms     | 1| 7 | 3.4| 0.9 |
|  Bathrooms    | 1 | 9 | 2.5 | 1.2 | 
| Price | $20,000 | $1,400,000 |$382,910 | $209,361      |  


Plotting by location:
![houses by long, lat](https://raw.githubusercontent.com/10brink/10brink.github.io/master/geoplot.png)

Plotting prices by zipcode:
![bplot by zipcode](https://raw.githubusercontent.com/10brink/10brink.github.io/master/bplotaa.png)

![AA zipcodes](https://raw.githubusercontent.com/10brink/10brink.github.io/master/aazip.gif)


I didn't end up using zipcode or geographic location in my model. long and lat were not predictive, and while zipcode was a little bit, it didn't make sense to me to use such arbitrary boundaries .




