---
layout: post
title: Using Pandas
published: true
---


Hi, I'm Nick, an aspiring data scientist, and I will be blogging about my experience at the Metis data science bootcamp in Chicago. In the first week, we downloaded, cleaned and analyzed NYC subway data to see what patterns can be found in the data. 

NYC has made the turnstile data publicly available on the [MTA website](http://web.mta.info/developers/turnstile.html). Unfortunately, the data is not exactly clean or user friendly, you can see it is in a .txt csv (comma separated values) file. We quickly learned that a large part of data science work is making sure that the data is clean and in a usable format. I will show you how I did this using python and pandas. Pandas (from the term panel data) is a library for python that provides data structures and makes working with relational data easy. 

### Importing data from the web

First we need to import the data into a pandas dataframe. My partner and I wrote this function that takes the date (mm/dd/yyyy) and the number of weeks and returns a data frame:

```python
def combine_mta_data(start_date, number):
    '''enter date mm/dd/yy (must be a Saturday, check http://web.mta.info/developers/turnstile.html for reference)
    and number of weeks (including starting date week)'''
    mod_date = pd.to_datetime(start_date)
    url = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_' + str(mod_date.strftime('%y%m%d')) + '.txt'
    df = pd.read_csv(url)
    for i in range(number-1):
        mod_date += pd.Timedelta(days=7)
        url = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_' + str(mod_date.strftime('%y%m%d')) + '.txt'
        df = pd.concat([df,pd.read_csv(url)], ignore_index=True)
    df.sort_values(by = ['C/A', 'SCP', 'DATE'], inplace = True)
    df.reset_index(inplace = True, drop = True)
    return df
  ```
    
Run the function with your desired start date and number of weeks, I used April 22 2017 and four weeks.  
```python
start_date = '04/22/2017' 
df = combine_mta_data(start_date, 4)
```

### Cleaning the data

If you look at the columns in the data frame (using `df.columns`), you'll notice that one of them has a lot of spaces in the name. We can easily remove this using the python function `.strip()`. While we're at it, let's remove any duplicate entries.
```python
df = df.drop_duplicates()
df.columns = df.columns.str.strip() 
```
You might also notice that there are two separate columns for date and time of day. Let's go ahead and combine those into one column, I named it 'TIMESTAMP' (make sure you import datetime first).

```python
df['TIMESTAMP'] = pd.to_datetime((df.DATE + ' ' + df.TIME), format='%m/%d/%Y %H:%M:%S')
```
I also want to make a column for day of the week.
```python
weekdays = ['MON','TUE','WED','THU','FRI','SAT','SUN']

df['DOF'] = [weekdays[dt.datetime.strptime(dstring,'%m/%d/%Y').weekday()] for dstring in df.DATE.tolist()]
# DOF = "day of week"
```
Another thing I noticed when scrolling through the data is that, while the data is usually reported in four-hour intervals, when those occur is not always uniform, and some turnstiles were reporting much more frequently. To solve this, we create another column with "bins" to put each row into, in four hour intervals. Thanks to [Lauren Oldja](http://laurenoldja.net/blog/open-data/mta-data-cleaning) for this idea. 

```python
bins = [-1, 4, 8, 12, 16, 20, 24]
df ['HOUR'] = [r.hour for r in df.TIMESTAMP] #hour of day
df['hourbin'] = pd.cut(df['HOUR'], bins)

```
We also recognized that numbers of entries in exits were cumulative counts, since we can see that they are in the millions and keep increasing. To get the counts for each interval, we can use the pandas function `.diff()`.

For this we wrote another function, find_deltas, that creates two new columns from the differences in the entries and exits columns. Later we found that some of the turnstiles were counting down, and some reset to 0 at seemingly random times. To account for this we used the absolute value of the difference, and any values that were abnormally large we reset to 0 (since this is counting each individual turnstile, it is unlikely that one turnstile would have more than 10000 entries in four hours). We also set the value to 0 anytime the turnstile changed in the dataframe --taking the difference in values between two different turnstiles doesn't make sense.


```python
def find_deltas(df):
    #creates two new columns, from the difference on the entries and exits column
    df['delta_entry']=abs(df.ENTRIES.diff())
    df['delta_exit']=abs(df.EXITS.diff())
  
    for i in range(0,df.index.max()) :
        #if (df.iloc[i,6]==start_date) & (df.iloc[i,7]== '00:00:00'):
        if (df.iloc[i, 2] != df.iloc[i-1,2] )| (i ==0) :
            df.loc[i, 'delta_entry']=0
            df.loc[i, 'delta_exit']=0
        if ( df.loc[i, 'delta_exit'] >10000) :
            df.loc[i, 'delta_exit']=0     
           
        if  df.loc[i, 'delta_entry'] >10000:
            df.loc[i, 'delta_entry']=0
            
find_deltas(df)
```
One more thing, let's add the entry and exit counts to get the total traffic:

```python
df['TRAFFIC']= df['delta_entry']+df['delta_exit']
```

### Data Exploration

Now that the tedious work of cleaning and managing the data is done, let's see what the five busiest stations were by total traffic:

```python
df.groupby(['STATION'])[ 'TRAFFIC'].sum().nlargest(5)
```
This code groups the rows by station, takes the sum of the traffic for every row, and shows us the five largest.

| STATION  | TRAFFIC   |
| -------- | ----: |
| 34 ST-PENN STA | 7,500,052|
| GRD CNTRL-42 ST| 6,793,625|   
| 34 ST-HERALD SQ | 5,960,562   |   
| 23 ST    | 5,299,998 |
| 14 ST-UNION SQ    | 5,030,327|   


What if we wanted to see the busiest times and days by station?

```python
df.groupby(['STATION', 'hourbin', 'DOF'])['delta_entry', 'delta_exit', 'TRAFFIC'].sum().nlargest(5, 'TRAFFIC')
```
This code groups the data by station as well as hour bin (four hour intervals) and day of week. We'll show the entries and exits but sort by total traffic. 

			
|STATION| hourbin| DOF	|	delta_entry	|delta_exit	|TRAFFIC|
|--|--|--|--|--|--|
|34 ST-PENN STA	|(16, 20]	|WED|	276,241|	344,637|	620,878|
|||TUE	|268,276	|348,503|	616,779|
|||THU	|258,329	|339,546|	597,875|
|||MON|	256,919|	329,947|	586,866|
|||FRI|	249,860|	308,534|	558,394|

Not surprisingly, the busiest times are during the evening rush, on weekdays, at Penn Station.
