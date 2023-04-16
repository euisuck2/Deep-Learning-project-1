# Capstone-Team-Air

## Overview of the project
The project aims to perform a development of an Airbnb price prediction model, which can provide hosts with a better understanding of the market and help them set competitive prices for their listings. The Airbnb platform is a popular way for hosts to offer affordable and unique accommodation choices, and can provide monetary benefit from the additional income. However, hosts often struggle to determine the optimal price for their listings, leading to missed opportunities and lower earnings. To address this problem, we propose to build and test both machine learning and deep learning models that predict the price of an Airbnb listing based on a variety of factors such as location, property type, amenities, etc. The models will be trained on a dataset of historical Airbnb listings in London, taking into account both numeric and categorical variables, and will be evaluated using appropriate performance metrics. The development of such a model can provide insights into the underlying factors that influence Airbnb prices. This will help Airbnb hosts with an objective and data-driven way to price their listings, based on the unique characteristics of their respective listings, allowing them to maximize their earnings while remaining competitive in the market. Team Air has decided to experiment with several modelling techniques. In this repository, There are three main parts stemming from the data cleaning and pre-processing steps: 

1) Machine Learning 
2) CNN + Machine Learning
3) BERT + CNN + Deep Learning

## Script-Data Relationship Diagram


## Getting the data and data cleaning - Download the Google Drive Folder 
All datasets used in this repo are publicly available. Please download the Capstone folder, which has the datasets already stored in one folder and is made publicly available at https://drive.google.com/drive/folders/1XXY2yoEeRK7i43zANWpfetQNRhcAFT4w?usp=sharing.
This step is crucial as it allows you to download all colaboratory scripts and secures all working directories as intended in the scripts themselves. Alternatively, please refer to a "Data Sources" section below  to get the original URLs for each data file.
Note: All scripts are already mounted to this "Capstone" folder. However, if you are not using colaboratory environment, you may remove the following import code in the notebooks that have them:

- from google.colab import drive
- drive.mount('/content/drive')

## Machine Learning 
Once you have downloaded the capstone folder, to run the machine learning code in this repository, navigate to the parent directory where the Machine Learning.ipynb file is located and open it in Colaboratory. If you would like to use Jupyter Notebook, you'll need Jupyter Notebook installed on your local machine. From there, you can run each cell in the notebook sequentially to see the results of the machine learning model. Make sure to have all the required dependencies installed before running the notebook. Please refer to requirements.txt.

## CNN + Machine Learning 
Refer to README.md under CNN folder for more information.

## Step 4: BERT + CNN + Deep Learning


## Data Sources
All datasets used in this repo are publicly available.


### Primary sources

1) Listings dataset
- Detailed listing data for all London airbnb properties that provide names, text descriptions of properties, neighborhood locations, latitudes, longitudes, price and other relevant attributes pertinent to the sizes of properties and review scores. Each listing is uniquely tagged to its ID, along with its unique host ID. It has a column for uploaded image URLs for the CNN model to train on. 

2) Reviews dataset
- Detailed review data for all London listed airbnb properties. Data columns include review IDs, Listing IDs, date of review, the reviewer’s name and the review itself in text format.

3) Calendar dataset
- Detailed calendar data for all London listed Airbnb properties, which includes listing IDs, date of listing, whether listing was available at that point in time, price recommended by Airbnb Smart pricing tool, the maximum and minimum night requirements of listing.

For all three datasets:
- Location: http://insideairbnb.com/get-the-data.html
- Format: CSV
- Access Method: Download

### Secondary Sources

1) Population Density dataset
- Population data for London that includes the area name, inland area, total area, population per hectare, 2011 Census population figures for each area.
- Location:https://data.london.gov.uk/dataset/land-area-and-population-density-ward-and-borough
- Format: CSV
- Access Method: Download


2) Public Transport dataset
- London underground data including station IDs, latitudes, longitudes, name, zone
- Location: https://commons.wikimedia.org/wiki/London_Underground_geographic_maps/CSV
- Format: HTML
- Access Method: Web scraping


3) Leading Visitor Attraction dataset
- 2020 visitor attraction dataset which includes ranking of visited site, name of attraction, total visitors and area. 
- Location: https://commons.wikimedia.org/wiki/London_Underground_geographic_maps/CSV
- Format: HTML
- Access Method: Copy/Paste into excel and tagged each area of interest manually for longitude, latitude and borough


4) London boroughs property prices dataset
- 2021 average property prices by boroughs can be found within this dataset. 
- Location: https://www.gov.uk/government/statistics/uk-house-price-index-for-october-2021/uk-house-price-index-england-october-2021
- Format: CSV
- Access Method: Download


5) AVA dataset 
-  Aesthetic Visual Analysis (AVA) contains over 250,000 images along with a rich variety of meta-data including a large number of aesthetic scores for each image, semantic labels for over 60 categories as well as labels related to photographic style for high-level image quality categorization.
- Location: https://academictorrents.com/details/71631f83b11d3d79d8f84efe0a7e12f0ac001460
- Format: jpg,json,txt
- Access Method: Download


## Data Access
Licenses for data use and redistribution are respected. If you have any additional questions about our approach to data access or our compliance with licenses, please contact us at [Michwynn@umich.edu].

