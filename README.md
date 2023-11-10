# Google-play-reviews-scraper
Scraping reviews left on any app from the GPlay store

## Disclaimer
This code is meant to be used for educational purposes and I take no responsibility for what you do with the code.

## Code
### Let's import the libraries needed

```
'''
Put together by Issuecoding https://github.com/Issuecoding
'''

# Find app package names at https://www.fileplanet.com/apk
# Google play scraper library https://github.com/facundoolano/google-play-scraper

from google_play_scraper import app
from google_play_scraper import Sort, reviews, reviews_all
import pandas as pd
import csv
import os, glob
```
