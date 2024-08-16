---
template: blog-post
title: Collecting NRL Player Data Using Python
slug: /data-analysis/collecting-data-1/
date: 2024-08-16 09:34
description: Learn how to collect and store NRL player statistics using Python.
  This guide covers web scraping with Selenium and BeautifulSoup, handling
  missing data, and storing the data in a MySQL database using SQLAlchemy.
  Perfect for data enthusiasts and developers looking to automate sports data
  collection and analysis.
featuredImage: /assets/dall·e-2024-08-16-10.02.17-a-visually-engaging-image-combining-elements-of-sports-data-visualization-and-coding.-the-image-should-feature-a-graph-or-chart-with-sports-statistics.webp
---
# Collecting NRL Player Data Using Python

**Introduction**

In my latest project, I set out to collect and analyze player statistics from the National Rugby League (NRL). This blog post details the initial step of the project: gathering data from the NRL website using web scraping techniques and storing it in a MySQL database. By the end of this post, you'll have a clear understanding of how to use Python, Selenium, BeautifulSoup, and SQLAlchemy to automate data collection for a large-scale project.

**Project Overview**

The primary goal of this phase was to gather detailed player information from various NRL team pages. To achieve this, I used Python to build a script that navigates each team's page, scrapes relevant player details, and stores the data in a MySQL database. This involved becoming familiar with the data and how it is presented for each player and I had a few issues that I needed to overcome. This process involved multiple stages:

1. **Setting Up Web Scraping Tools**
2. **Navigating and Extracting Data**
3. **Handling Missing Data**
4. **Storing Data in a Database**

**Setting Up Web Scraping Tools**

To get started, I chose Selenium for navigating web pages and BeautifulSoup for parsing the HTML content. Selenium is particularly useful for interacting with dynamically generated content, which is common on sports websites like the NRL.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
import time
import random

# Path to the ChromeDriver executable
chrome_driver_path = "path_to_chromedriver"

# Set up the WebDriver
service = Service(chrome_driver_path)
driver = webdriver.Chrome(service=service)
```

With Selenium, I could automate the process of opening URLs, allowing the script to access player data from each team’s page.

**Navigating and Extracting Data**

Once the pages were loaded, I used BeautifulSoup to extract specific details like player names, positions, and teams. Here’s how I did it:

```
from bs4 import BeautifulSoup
import requests

# Define headers to mimic a browser request
headers_browser = {
    'User-Agent': 'Mozilla/5.0'
}

# Extracting player details
response = requests.get(player_url, headers=headers_browser)
soup = BeautifulSoup(response.content, 'html.parser')

player_name_element = soup.find('h1', class_='club-card__title')
```

This allowed me to parse the HTML content of each page and retrieve the required data fields.

**Handling Missing Data**

In real-world web scraping, not all data fields are always present. Some players did not have values for their height, weight or date of birth for example. This was particularly true for players who were new this season. I implemented a system to handle missing values gracefully, ensuring the integrity of the dataset:

```
default_values = {
    'Height:': None,
    'Date of Birth:': None,
    'Weight:': None,
    # Other attributes
}

player_attributes_data = [attr.text.strip() for attr in soup.find_all('dd')]
```

This approach assigns default values when specific attributes are missing, preventing errors during data processing.

**Storing Data in a Database**

After collecting and cleaning the data, I used SQLAlchemy to store it in a MySQL database. This ensures that the data is well-organized and easily accessible for future analysis.

```
from sqlalchemy import create_engine, text

engine = create_engine('mysql+mysqlconnector://username:password@host/database')

# Insert data into the database
player_df.to_sql('playerattributes', engine, if_exists='append', index=False)
```

This step marks the end of the data collection process, with all player information safely stored and ready for further analysis.

**N﻿ext Step**

H﻿aving collected the players attributes in this step, the next step in the data collection process is to create a table for the players game data. This will collect the data around the players stats in a game. For example, tries scored, tackles made, run metres and passes. 

**Conclusion**

This project exemplifies how Python’s powerful libraries can be combined to automate data collection and storage. By using Selenium, BeautifulSoup, and SQLAlchemy, I streamlined the process of gathering and managing large amounts of data. In future posts, I’ll delve into the next steps, including data analysis and visualization using this rich dataset.

Stay tuned as I continue this journey into data analysis and uncover valuable insights from the NRL data!

I﻿f you want to view the full file for this scraper it is available on my Github at https://github.com/nate-ph/nrl_data_extractor/blob/main/Player%20attributes%20scraper.py