# Google-play-reviews-scraper
Scraping reviews left on any app from the GPlay store

## Disclaimer
This code is meant to be used for educational purposes and I take no responsibility for what you do with the code.

## Code
### Let's import the libraries needed

´´´
'''
Put together by Issuecoding https://github.com/Issuecoding
'''

/# Find app package names at https://www.fileplanet.com/apk
/# Google play scraper library https://github.com/facundoolano/google-play-scraper

from google_play_scraper import app
from google_play_scraper import Sort, reviews, reviews_all
import pandas as pd
import csv
import os, glob
´´´
### We create a function to extract the comments
´´´
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

´´´
