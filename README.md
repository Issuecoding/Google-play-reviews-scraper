# Google-play-reviews-scraper
Scraping reviews left on any app from the GPlay store

## Disclaimer
This code is meant to be used for educational purposes and I take no responsibility for what you do with the code.

## Code
### Let's import the libraries needed

```python
# Find app package names at https://www.fileplanet.com/apk
# Google play scraper library https://github.com/facundoolano/google-play-scraper

from google_play_scraper import app
from google_play_scraper import Sort, reviews, reviews_all
import pandas as pd
import csv
import os, glob
```

### Create a function to extract the comments
```python
def extract_comments(language, country, app_package_name):
    it = iter(range(1,6)) # to iterate the following loop over the selected star ratings
    for x in it: # (1,6) being star ratings from 1 to 5
        result = reviews_all(
            app_package_name,
            sleep_milliseconds=0, # defaults to 0
            lang=language, # defaults to 'en'
            country=country, # defaults to 'us'
            sort=Sort.NEWEST, # defaults to Sort.MOST_RELEVANT
            filter_score_with=x # defaults to None(means all score)
        )
    
        if result == []: # Skips x value in case there are no reviews at this score
            continue
        else:
            toCSV = result
            # Add line saying if result for x=x are 0, then skip to next value of x
            keys = toCSV[0].keys()
            filename = 'reviews_' + language + '_' + str(x) + '.csv' # eg. 'reviews_it_5.csv'
        with open(filename, 'w', encoding='utf8', newline='') as output_file:
            dict_writer = csv.DictWriter(output_file, fieldnames=toCSV[0].keys())
            dict_writer.writeheader()
            dict_writer.writerows(toCSV)
```
### Merge all the created CSVs into one
```python
# Merge CSVs in one
# https://blog.softhints.com/how-to-merge-multiple-csv-files-with-python/

path_1 = ''
path_2 = 'D:/Your location of choice'

def merge_CSVs(language):
    filename = 'reviews_' + language
    all_files = glob.glob(os.path.join(path_1, filename + '*.csv'))
    df_from_each_file = (pd.read_csv(f, sep=',') for f in all_files)
    df_merged = pd.concat(df_from_each_file, ignore_index=True)
    df_merged.to_csv(path_2 + '/All_reviews_' + language + '.csv', index=False)
```
Let's add a language column
```
# Add language column
def add_language(language):
    filename = 'All_reviews_' + language + '.csv'
    all_reviews_df = pd.read_csv(path_2 + '/' + filename)
    all_reviews_df['language'] = language
    all_reviews_df.insert(loc=len(all_reviews_df.columns), column='platform', value='Android') # adding platform column
    all_reviews_df.insert(3, 'title', '') # adding title column to make df equal to iOS df
    all_reviews_df.rename(columns={'at': 'date'}, inplace=True) # changing column name from 'at' to 'date'
    return all_reviews_df, filename
```
Let's export the results as a CSV
```
# Export results to csv
def export(all_reviews_df, filename):
    all_reviews_df.to_csv(path_2 + '/' + filename, index=False)
```
And delete remaining single CSVs
```
# Delete remaining single rating files
def remove_single_CSVs():
    all_single_ratings = glob.glob(os.path.join(path_1, 'reviews*.csv'))
    for x in all_single_ratings:
      os.remove(x)
```
And this is the master function which directs how the other functions should flow
```
# Master command
def master_command(language, country, app_package_name):
    extract_comments(language, country, app_package_name)
    merge_CSVs(language)
    all_reviews_df, filename = add_language(language)
    export(all_reviews_df, filename)
    remove_single_CSVs()   
```
So we start it all by inputting the desired app package name here
```
master_command('en', 'US', 'com.spotify.music') # (language, country, package name)
```
