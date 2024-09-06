---
template: blog-post
title: "Using Python and SQL Together in VS Code for NRL Player Data: Creating a
  Database and Inserting Player Stats"
slug: /data-analysis/using-python-and-sql/
date: 2024-09-06 09:00
description: Learn how to use Python and SQL together in VS Code to scrape NRL
  player statistics, create a database, and insert data into tables. A
  step-by-step guide tailored for data projects, combining web scraping, data
  storage, and analysis.
featuredImage: /assets/dall·e-2024-09-06-09.07.19-a-visually-engaging-image-showcasing-python-and-sql-integration-for-sports-data-analysis-specifically-for-national-rugby-league-nrl-player-statisti.webp
---
### **Using Python and SQL Together in VS Code for NRL Player Data: Creating a Database and Inserting Player Stats**

In this blog post, I’ll walk you through how I use **Python** with **SQL** in **Visual Studio Code (VS Code)** to create a database, define tables, and insert data. Specifically, I'll be demonstrating how I collect, store, and analyze **NRL (National Rugby League)** player statistics using a web scraping project. By the end, you’ll know how to create a structured database to house player stats and how to efficiently insert and query data.

If you’re interested in data analysis, Python, and sports, this tutorial will show you how these tools can be combined to handle player statistics with ease.

- - -

### **Step 1: Set Up the Environment**

First, you'll need to set up your environment by installing essential Python libraries. These include **SQLAlchemy** for database management, **BeautifulSoup** and **Selenium** for web scraping, and **MySQL Connector** to connect Python to a MySQL database.

Open a terminal in **VS Code** and run the following command:

```
pip install sqlalchemy mysql-connector-python beautifulsoup4 selenium pandas

```

This will install the necessary packages:

* **SQLAlchemy**: A toolkit for SQL databases.
* **MySQL Connector**: A MySQL driver for Python.
* **BeautifulSoup & Selenium**: Libraries to scrape NRL player data from websites.
* **Pandas**: A data manipulation tool for processing scraped data.

- - -

### **Step 2: Connecting to MySQL Database**

We’ll connect Python to a MySQL database where we'll store the NRL player stats. The first step is to establish a connection.

```
from sqlalchemy import create_engine

# Database credentials
database_type = 'mysql'
database_driver = 'mysqlconnector'
username = 'root'
password = 'your_password'
host = 'localhost'
port = '3306'
database_name = 'nrl_stats'

# Connect to the MySQL database
engine = create_engine(f'{database_type}+{database_driver}://{username}:{password}@{host}:{port}/{database_name}')

```

This connection allows us to send SQL queries from Python directly to our MySQL database.

- - -

### **Step 3: Create the Database (Optional)**

If the database doesn’t exist yet, you can create it from within Python.

```
from sqlalchemy import text

# Create the database
create_db_query = 'CREATE DATABASE IF NOT EXISTS nrl_stats;'
with engine.connect() as connection:
    connection.execute(text(create_db_query))

```

This step will ensure that your database for storing player stats is set up and ready to go.

- - -

### **Step 4: Define and Create Tables for Player Statistics**

Next, let’s define and create the tables for storing NRL player statistics. Each player will have attributes like name, team, position, tries, goals, and more.

Here’s how to create a basic player stats table:

```
# Define the player statistics table
create_table_query = ''' 
CREATE TABLE IF NOT EXISTS player_stats (
    player_id INT AUTO_INCREMENT PRIMARY KEY,
    player_name VARCHAR(100),
    team_name VARCHAR(100),
    position VARCHAR(50),
    games_played INT,
    tries INT,
    goals INT,
    points INT
);
'''

# Execute the query
with engine.connect() as connection:
    connection.execute(text(create_table_query))

```

This table structure is designed to store basic player stats, but you can expand it based on the data you’re collecting (such as tackles, run meters, etc.).

- - -

### **Step 5: Scrape Player Data and Insert into the Database**

Using **BeautifulSoup** and **Selenium**, you can scrape NRL player data from a website and insert it into the database. Here’s a simplified example of how to scrape and insert data into the table we just created.

```
from bs4 import BeautifulSoup
from selenium import webdriver
import pandas as pd

# Start a Selenium web driver
driver = webdriver.Chrome()

# Scrape the webpage
url = 'https://example-nrl-players.com/stats'
driver.get(url)
soup = BeautifulSoup(driver.page_source, 'html.parser')

# Assuming we scrape the data and process it into a DataFrame
player_data = pd.DataFrame({
    'player_name': ['Player A', 'Player B'],
    'team_name': ['Team X', 'Team Y'],
    'position': ['Forward', 'Back'],
    'games_played': [10, 12],
    'tries': [5, 8],
    'goals': [0, 3],
    'points': [20, 30]
})

# Insert the data into the MySQL database
with engine.connect() as connection:
    for index, row in player_data.iterrows():
        insert_query = f'''
        INSERT INTO player_stats (player_name, team_name, position, games_played, tries, goals, points)
        VALUES ('{row['player_name']}', '{row['team_name']}', '{row['position']}', {row['games_played']}, {row['tries']}, {row['goals']}, {row['points']});
        '''
        connection.execute(text(insert_query))

```

Here, **BeautifulSoup** and **Selenium** are used to scrape data from an NRL stats page, and **Pandas** handles the data before it's inserted into the database.

- - -

### **Step 6: Querying the Database**

Now that the data is in the database, you can query it to retrieve useful information such as the top try-scorers, or the average points per game.

```
# Query player statistics
query = '''
SELECT player_name, team_name, tries, goals, points 
FROM player_stats 
ORDER BY points DESC;
'''

with engine.connect() as connection:
    result = connection.execute(text(query))
    for row in result:
        print(row)

```

This will display the players sorted by total points, with their respective try and goal stats.

- - -

### **Step 7: Data Visualization (Optional)**

Once you have the data in your database, you can visualize it using **Pandas** or **Matplotlib** to gain deeper insights. For example, you can create bar charts of the top try-scorers or analyze team performance.

```
import matplotlib.pyplot as plt

# Query the data and load into a Pandas DataFrame
query = 'SELECT player_name, tries FROM player_stats ORDER BY tries DESC LIMIT 10;'
top_try_scorers = pd.read_sql(query, engine)

# Plot the data
top_try_scorers.plot(kind='bar', x='player_name', y='tries', title='Top 10 Try Scorers')
plt.show()

```

- - -

### **Best Practices for Python and SQL Integration**

Here are some best practices for working with Python and SQL:

1. **Use Environment Variables**: Store sensitive data like database credentials in environment variables instead of hardcoding them.
2. **Batch Inserts**: When dealing with large datasets, use batch inserts to reduce the number of individual database queries.
3. **Indexing**: Ensure that your database tables are properly indexed to optimize query performance, especially when dealing with large amounts of player data.
4. **Error Handling**: Always handle errors when interacting with databases using try-except blocks. This ensures your script doesn’t fail abruptly.
5. **Clean and Normalize Data**: When scraping data from websites, ensure it’s cleaned and normalized before inserting it into the database to avoid inconsistent data.

- - -

### **Final Thoughts**

Combining **Python** and **SQL** in **VS Code** is a powerful way to manage your NRL player data. Using **SQLAlchemy** for database management and **BeautifulSoup/Selenium** for web scraping allows you to build a robust system for tracking player performance. Whether you’re analyzing player trends or creating visualizations, this workflow will enhance your data analysis projects.