# Python ETL Pipeline to load reference data into your database

Before we start coding the pipeline, we will cover a few basic topics: how to open and read data from flat files, so that we can store and process the data. We will perform checks on the files before processing them.

This ETL pipeline will enable you to:

Understand OS module to iterate over directories
Perform certain checks on files
Read files with Pandas and Write data to a database
Store data in a database table

1. The Setup

I have prepared a PostgreSQL environment which will serve as the destination where we will load our data. 
We grab the password and username from the environment variables and define the database details such as server, database, and port as local variables.

```ruby
from sqlalchemy import create_engine
import pyodbc
import pandas as pd
import os
pwd = os.environ['PGPASS']
uid = os.environ['PGUID']
server = "localhost"
db = "AdventureWorks"
port = "5432"
dir = r'C:\Users\Jupyter Notebook\Excel to Postgres - load reference data to database with Python ETL Pipeline'
```

2. Read Files with OS Module

Python provides various options to iterate over files in a directory. You can pick an approach that best fits your needs. 
We will use “listdir()” function from the OS module. This function returns the list of files and subdirectories present in the given directory. 
We can filter the list to get only the files using “os.path.isfile()” function. 
Also, we can limit our criteria to certain type of files for example, we can use Python String “endswith()” method. 
The “endswith()” method returns True if a string ends with the xlsx suffix. Once we get hold of the file, we can build a complete file path with “path.join()” method. 
The join method combines the file path and filename to give us complete path. Our Extract function will be based on this approach.

3. The Extract

The first step is to read the Excel files as pandas DataFrames. We iterate over the directory and get the files name. 
We will use the file name as the table name, So, make sure to name your files appropriately. 
Let us split the filename to grab the name without the extension.
We only have data in excel files so let us check if file extension is xlsx. We do not want to process any other type of file in case someone copies csv or text file in this directory.
We join the directory and filename with join function from OS dot path. This gives us the complete file path.
Now we have the complete file path. Let us read the file in a pandas DataFrame with “read_excel()”. 
We call the load function, which we will code next, and to this function we pass the DataFrame and the file name without extension.

```ruby
def extract():
    try:
        # starting directory
        directory = dir
        # iterate over files in the directory
        for filename in os.listdir(directory):
            #get filename without ext
            file_wo_ext = os.path.splitext(filename)[0]
            # only process excel files
            if filename.endswith(".xlsx"):
                f = os.path.join(directory, filename)
                # checking if it is a file
                if os.path.isfile(f):
                    df = pd.read_excel(f)
                    # call to load
                    load(df, file_wo_ext)
    except Exception as e:
        print("Data extract error: " + str(e))
```

The Load

In the load function we persist the data as a database table. 
Table name is provided as an argument to this function. 
To save data to our database let us create a connection to PostgreSQL with SQLAlchemy. 
We supply it the connection details from local variables. 
Pandas makes it a breeze to load data into a SQL database with pandas “to_sql()” function. 
We process the DataFrame passed to our load function. We will follow the truncate and load approach as it is simple and it replaces the table with each run. 
This can be your staging environment where you pull in new data on a given interval. 
From here you can load this data into a presentation layer for you reporting. 
The code below demonstrates how to connect and store data in a PostgreSQL database using Pandas.


```ruby
#load data to postgres
def load(df, tbl):
    try:
        rows_imported = 0
        engine = create_engine(f'postgresql://{uid}:{pwd}@{server}:{port}/{db}')
        print(f'importing rows {rows_imported} to {rows_imported + len(df)}... ')
        # save df to postgres
        df.to_sql(f"stg_{tbl}", engine, if_exists='replace', index=False)
        rows_imported += len(df)
        # add elapsed time to final print out
        print("Data imported successful")
    except Exception as e:
        print("Data load error: " + str(e))

try:
    #call extract function
    df = extract()
except Exception as e:
    print("Error while extracting data: " + str(e))
```




