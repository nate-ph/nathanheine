---
template: blog-post
title: Automating NRL Player Data Collection with Python, BeautifulSoup,
  Selenium, and SQLAlchemy
slug: /data-analysis/collecting-data-2/
date: 2024-08-18 11:06
description: Learn how to automate the collection of NRL player data using
  Python, BeautifulSoup, Selenium, and MySQL. This step-by-step guide shows you
  how to scrape and insert game statistics into a database, making it easy to
  analyze and utilize player performance data
featuredImage: /assets/dall·e-2024-08-18-11.06.58-a-visually-engaging-image-representing-the-automation-of-nrl-player-data-collection.-the-image-should-include-a-stylized-computer-screen-displaying-a-.webp
---
### Automating NRL Player Data Collection with Python, BeautifulSoup, Selenium, and SQLAlchemy

In the world of data science, automating data collection processes is a crucial skill, especially when dealing with large datasets from dynamic websites. In this blog post, I'll walk you through a Python script I developed to scrape NRL player data and store it in a MySQL database. This process involves using several powerful libraries: BeautifulSoup for parsing HTML, Selenium for navigating dynamic content, and SQLAlchemy for managing the database interaction.

#### Project Overview

The primary goal of this project is to automate the collection of NRL player statistics from multiple teams and store this data in a structured format within a MySQL database. This will allow for easy access and analysis of player performance metrics across the league.

#### Tools and Libraries

* **Python**: The main programming language used for the script.
* **BeautifulSoup**: A library for parsing HTML and extracting data.
* **Selenium**: A tool for automating web browsers, crucial for interacting with dynamic web pages.
* **SQLAlchemy**: A toolkit for SQL and database management.
* **Pandas**: A data manipulation library used to structure the data before inserting it into the database.
* **MySQL**: The database where the collected data is stored.

#### The Code

The script begins by setting up a connection to the MySQL database. It then defines the schema for the table where the data will be stored:

```
create_table = ''' CREATE TABLE IF NOT EXISTS currentgamedata (
    gameid SERIAL PRIMARY KEY,
    UrlID VARCHAR (200),
    round VARCHAR(10),
    opponent VARCHAR(50),
    score VARCHAR(10),
    position VARCHAR(20),
    minutes_played INT,
    tries INT,
    goals VARCHAR(200),
    one_point_field_goals INT,
    two_point_field_goals INT,
    points INT,
    kicking_metres INT,
    forced_dropouts INT,
    try_assists INT,
    linebreaks INT,
    tackle_breaks INT,
    post_contact_metres INT,
    offloads INT,
    receipts INT,
    tackles_made INT,
    missed_tackles INT,
    total_running_metres INT,
    hit_up_running_metres INT,
    kick_return_metres INT,
    FOREIGN KEY (UrlID) REFERENCES playerattributes(UrlID)
);'''
```

This schema includes various metrics that reflect player performance, such as tries, goals, and tackles made.

#### Web Scraping with Selenium

The next step is to use Selenium to automate the process of navigating to each team's webpage and extracting the URLs of individual player profiles:

```
NRL_URL = [sharks, bulldogs, dolphins, ...]
# Initialize an empty list to store player URLs
hrefs = []

try:
    # Loop through each team URL
    for url in NRL_URL:
        # Open the URL
        driver.get(url)

        # Wait for the page to load and JavaScript to execute
        time.sleep(random.uniform(2, 5))  # Adjust based on your connection speed

        # Find all elements with the specified class name
        player_links = driver.find_elements(By.CLASS_NAME, 'card-themed-hero-profile')

        # Extract href attributes
        hrefs.extend([link.get_attribute('href') for link in player_links])

finally:
    # Close the WebDriver
    driver.quit()
```

This block of code ensures that all player URLs are collected efficiently, with the use of random sleep intervals to mimic human browsing behavior and avoid being flagged as a bot.

#### Parsing Data with BeautifulSoup

Once the URLs are collected, BeautifulSoup is used to parse the HTML content and extract the relevant data:

```
response = requests.get(url, headers=headers_browser)
if response.status_code == 200:
    soup = BeautifulSoup(response.content, 'html.parser')
    target_header_text = " 2024 Season - By Round "
```

The data is then cleaned and structured using Pandas, preparing it for insertion into the MySQL database. This step created a number of issues around the columns for players where some players were missing data depending on their position. Please refer to my code in Github to learn how to overcome these issues. 

#### Inserting Data into MySQL

Finally, the structured data is inserted into the MySQL database:

```
if not all_player_data_df.empty:
    try:
        all_player_data_df.to_sql('currentgamedata', engine, if_exists='append', index=False)
        print(f'Successfully inserted data to currentgamedata. {len(rows)} inserted into table.')
    except SQLAlchemyError as e:
        print(f'Error inserting data {e} {url}')
else:
    print('No new data to insert')
```

This ensures that all collected data is stored safely and can be accessed for future analysis.

#### Conclusion

This project highlights the power of automation in data collection, especially when dealing with large and complex datasets. By leveraging Python and its extensive libraries, we can streamline the process of collecting, cleaning, and storing data, enabling more efficient analysis and decision-making.

Whether you're a data scientist, sports analyst, or just a Python enthusiast, automating data collection is a valuable skill that can save time and improve the accuracy of your analysis. I hope this walkthrough inspires you to explore similar projects in your own work.

Feel free to reach out if you have any questions or need further clarification on any part of the code. Happy coding!