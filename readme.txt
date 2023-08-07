# Map with Sales Data Visualization

This project involves processing sales data, geocoding locations, and generating an interactive map with sales information. The map displays markers representing sales locations and their corresponding coordinates.

## Project Overview

The project involves the following steps:

1. **Data Processing**: Extracting area and location information from sales data using regular expressions and data cleaning techniques.

2. **Geocoding**: Utilizing the Google Maps API to geocode location names and obtain latitude and longitude coordinates.

3. **Map Generation**: Creating an interactive map using the Folium library to display sales locations as markers on the map.

4. **Auto Commit and Push**: Automating the commit and push process to a GitHub repository using a batch script.

## Installation

Before running the project, ensure you have the following installed:

- Python 3.x
- pandas
- numpy
- sqlalchemy
- pymysql
- mysql-connector-python
- googlemaps
- folium

You can install the required Python packages using the following command:

```bash
pip install pandas numpy sqlalchemy pymysql mysql-connector-python googlemaps folium


Also before running the project make sure you have installed Git and have configured your Github credentials so the automatic commits and pushes can be performed. 
If not comment out the following:

import subprocess

result = subprocess.run("auto_commit.bat", shell=True, capture_output=True, text=True)
print(result.returncode)
print(result.stdout)
print(result.stderr)

## Clone the Github repository
If you have properly installed Git you can clone the repository of the project so you will have an up to date version of it.
you can do it using the following commands in the command line:
git clone https://github.com/franky2123/mapWithSales.git
cd mapWithSales

if you want to use your own google api just change the key of the api call in this command:
gmaps = googlemaps.Client(key='your_key')

Now you are ready to run all the code cells!


## Code description

In this section of the documentation there will the explanation of the code that achieves what is mentioned above. It will be analyzed cell by cell for the notepad Map_generator.

-In the first cell there are all the necessary library imports for the code to run properly all the commands. 

-In the second cell a connection to the database is made and the table closing_board is retrieved and stored in a dataframe with the same name. 
This dataframe contains all housing sales data for ongoing, confirmed and cancelled deals. At the end the connection with the database is closed.

-In the third cell the limit of the rows and the columns that are displayed for every dataframe are changed to 500 for both so all the dataframe can be viewed.

-In the fourth cell a function named extract_location is defined which extracts all the locations from the listing title (name). 
At first it replaces all the words that are not able to be extracted using regex expressions and then some patterns that were noticed to contain the square meters. 
Then the function removes from the name all the hyphens, dashes and other symbols that are not needed as well as multiple spaces because after all the removals it was observed that there were a lot of multiple spaces.  
Finally, it returns the location. In the end of the cell the function is applied in the dataframe closing_board and in the column name end for each row a location is created from the name and stored in a new column named location in the same row. 
The name is not edited.

-In the fifth cell the coordinates of every location are retrieved using the google geocoding API.  
At first the API is initialized using the key of the API already created in the Google API website and it is stored in the gmaps variable. 
Next all the deals that are not cancelled are assigned to closing_for_map variable so that the cancelled deals that are not supposed to be displayed in the map won’t delay the process. 
Then all the unique locations are being extracted and the inserted in the locations_df dataframe so the API won’t be called multiple times for the same location. 
The country code is being set to GR because all the locations in the table are from Greece. 
If this code is going to be used in a different table that does not contain locations only from Greece, the code should be changed in the country needed or the filter should be removed. 
After that a for loop is initiated so the API will run for every value of the dataframe and in the loop the location, that is being put in the location_name variable in every loop, is being geocoded from the API using the country_code as region. 
Then is there are any results returned the coordinates are inserted in the locations_df dataframe. If the location wasn’t found a message is displayed so the user can manually find the coordinates for that location. 
If an error occurs an exception is thrown and an error message is being displayed with the error code. After that using the sleep from the time library, we delay the script for 0.002 seconds so the API’s limit of the 50 requests per second is not met. 
In the end, after the loop the locations_df is displayed so the user can see if there are any errors. (For your convenience you can use the command locations_df[locations_df[“latitude”].isna()] so you can is only the NaN values.)

-In the sixth cell a dataframe errors is created that contains the locations and their coordinates for those that were not found by the API. This must be filled manually is a new NaN location is inserted in the table that is not already in the dataframe. 
(The code will still work but the values that the API didn’t find will be removed from the map so the map script can be executed.)

-In the seventh cell using a for loop the coordinates of the locations manually inserted in the errors dataframe are inserted in the corresponding locations in the locations_df
 by finding the index of the row that that location is in the dataframe and using that inserting the coordinates in that row.
-In the eighth cell the NaN values are being dropped so an error won’t occur in the next step and the two dataframes (closing_for_map and cleaned_df) are being merged using the .merge function and the left join method of merging so every row of the original dataframe (without the cancelled deals) are matched with the coordinates of there location. This is necessary because previously the locations_df contained the unique locations so the API would not be called multiple timed for one location. 
-In the last cell the map is created. 
At the top of the cell the map is initiated and the started locations that is going to be displayed as well as the zoom that the map is going to have at the beginning are being set. 
Then we create the MarkerCluster object so that pointers in the map that are close to each other are grouped for better visualization of the data. 
Then by using a for loop, for every location a pointer is set in the locations coordinates and a popup that will pop up when it is clicked and it will display the title of the listing. 
Then the map is saved as an html file named index because that is the name that the site hosting service netlify is recognizing. 
Then by using the library subprocess a script is executed in the command line that pushes the project in the github repository so the netlify site is up to date with the updates. 
At last the map is displayed using IFrame.

-The auto_commit.bat script contains the following commands:

@echo off : This command blocks the command line from displaying all the running process of the script for convenience.

git remote add origin https://github.com/franky2123/mapWithSales : This command configures the Git repository to have a remote named "origin" that points to the specified URL (GitHub repository). 
The remote is used for syncing changes between your local repository and the remote repository on GitHub. (It needs to be changed if the repository is changed)

git add . : This command stages all changes in the current directory and its subdirectories for the next commit. The . refers to the current directory.

git commit -m "Auto commit on file change" : This command creates a commit with the staged changes. The -m flag is used to provide a commit message enclosed in double quotes. 
The commit message is a brief description of the changes made in the commit and it can be changed when needed.

git push origin master : This command pushes the committed changes from your local repository to the remote repository on GitHub. 




