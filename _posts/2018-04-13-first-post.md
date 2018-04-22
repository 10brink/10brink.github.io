---
layout: post
title: Metis Bootcamp
---


Hi, I'm Nick, an aspiring data scientist, welcome to my blog! I'm currently attending the Metis data science bootcamp in Chicago. I've already learned a lot! In the first week, we downloaded, cleaned and analyzed NYC subway data to see what patterns can be found in the data. 

NYC has made the turnstile data publicly available on the [MTA website](http://web.mta.info/developers/turnstile.html). Unfortunately, the data is not exactly clean or user friendly, can see it is in a txt csv (comma seperated values) file. We quickly learned that a large part of data science work is making sure that the data is clean and in a usable format. I will show you how I did this using python and pandas. Pandas (from the term panel data) is a library for python that provides data structures and makes working with relational data easy. 

###Importing data from the web
First we need to import the data into a pandas dataframe. My partner and wrote this function that takes the date (mm/dd/yyyy) and the number of weeks and returns a data frame:

'''python
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
    
    '''
    
run the function with your desired start date and number of weeks, I used April 22 2017 and four weeks.  
'''python
start_date = '04/22/2017' 
df = combine_mta_data(start_date, 4)
'''

###Cleaning the data

If you look at the columns in the data frame (using '''df.columns'''), you'll notice that one of them has a lot of spaces in the name. We can easily remove this using the python function '''.strip()'''. while we're at it, let's remove any duplicate entries.
'''python
df = df.drop_duplicates()
df.columns = df.columns.str.strip() 
'''
You might also notice that there are two seperate columns for date and time of day. Let's go ahead and combine those into one column, I named it 'TIMESTAMP' (make sure you import datetime first).

'''python
df['TIMESTAMP'] = pd.to_datetime((df.DATE + ' ' + df.TIME), format='%m/%d/%Y %H:%M:%S')
'''
I also want to make a column for day of the week.
'''python
weekdays = ['MON','TUE','WED','THU','FRI','SAT','SUN']

df['DOF'] = [weekdays[dt.datetime.strptime(dstring,'%m/%d/%Y').weekday()] for dstring in df.DATE.tolist()]
# DOF = "day of week"

'''

