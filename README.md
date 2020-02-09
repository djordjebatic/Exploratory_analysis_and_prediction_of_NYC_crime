# Exploratory analysis and prediction of NYC crime

Crime rates in New York City spiked in the 1980s and early 1990s as the crack epidemic hit, but they have been dropping since 1991, and, as of 2017, they are among the lowest of major cities in the United States. During the 1990s, the New York City Police Department (NYPD) adopted CompStat, broken windows policing, and other strategies in a major effort to reduce crime. The city's dramatic drop in crime has been variously attributed to a number of factors, including the end of the crack epidemic, the legalization of abortion, and the decline of lead poisoning in children.

Today, the city is known more for its fashion and tourism than its criminal past. But, with rising wealth inequality, housing shortages, and a number of tourists flocking the city, there is still no scarcity of crime in the Big Apple. First part of this project is dedicated to analysing the data in order to gain insight in underlying patterns and trends in crime over the years. After we get a deeper understanding of our data we move on to predicting the type of crime most likely to happen in certain place and time. Combining the knowlege of crime patterns and probabilites of it happening we can help police allocate resources more efficiently and be better prepared for certain dangerous events.

## Getting Started
### Data

This dataset provides nearly 40 years of crime reports from across all of NYC's neighborhoods. There is 6.5 million recorded complaints and 35 fields describing date, time, place of occurance crime and various other data including age, sex and race of suspects and victims. Column descriptions can be seen in table below.

|Column |	Description |
| - | - |
|CMPLNT_NUM	|Randomly generated persistent ID for each complaint |
|CMPLNT_FR_DT|	Exact date of occurrence for the reported event (or starting date of occurrence, if CMPLNT_TO_DT exists)|
|CMPLNT_FR_TM	|Exact time of occurrence for the reported event (or starting time of occurrence, if CMPLNT_TO_TM exists)|
|CMPLNT_TO_DT|	Ending date of occurrence for the reported event, if exact time of occurrence is unknown|
|CMPLNT_TO_TM|	Ending time of occurrence for the reported event, if exact time of occurrence is unknown|
|RPT_DT	|Date event was reported to police |
|KY_CD|	Three digit offense classification code|
|OFNS_DESC	|Description of offense corresponding with key code|
|PD_CD	|Three digit internal classification code (more granular than Key Code)|
|PD_DESC	|Description of internal classification corresponding with PD code (more granular than Offense Description)|
|CRM_ATPT_CPTD_CD	|Indicator of whether crime was successfully completed or attempted, but failed or was interrupted prematurely
|LAW_CAT_CD	|Level of offense: felony, misdemeanor, violation |
|JURIS_DESC|	Jurisdiction responsible for incident. Either internal, like Police, Transit, and Housing; or external, like Correction, Port Authority, etc.|
|BORO_NM	|The name of the borough in which the incident occurred|
|ADDR_PCT_CD	|The precinct in which the incident occurred|
|LOC_OF_OCCUR_DESC|	Specific location of occurrence in or around the premises; inside, opposite of, front of, rear of|
|PREM_TYP_DESC|	Specific description of premises; grocery store, residence, street, etc.|
|PARKS_NM	|Name of NYC park, playground or greenspace of occurrence, if applicable (state parks are not included)|
|HADEVELOPT|	Name of NYCHA housing development of occurrence, if applicable|
|X_COORD_CD|	X-coordinate for New York State Plane Coordinate System, Long Island Zone, NAD 83, units feet (FIPS 3104)|
|Y_COORD_CD	|Y-coordinate for New York State Plane Coordinate System, Long Island Zone, NAD 83, units feet (FIPS 3104)|
|Latitude|	Latitude coordinate for Global Coordinate System, WGS 1984, decimal degrees (EPSG 4326) |
|Longitude|	Longitude coordinate for Global Coordinate System, WGS 1984, decimal degrees (EPSG 4326)|


Detailed information about dataset can be downloaded [here](https://data.cityofnewyork.us/api/views/qgea-i56i/files/b21ec89f-4d7b-494e-b2e9-f69ae7f4c228?download=true&filename=NYPD_Complaint_Incident_Level_Data_Footnotes.pdf).

### Algorithms used

#### 1) Random Forest Classifier
An ensemble learning method for classification, regression and other tasks that operates by constructing a multitude of decision trees at training time and outputting the class that is the mode of the classes of the individual trees. Random decision forests correct for decision trees' habit of overfitting to their training set.
#### 2) Extra Tree Classifier
An extremely randomized tree classifier. Extra-trees differ from classic decision trees in the way they are built. When looking for the best split to separate the samples of a node into two groups, random splits are drawn for each of the max_features randomly selected features and the best split among those is chosen. When max_features is set 1, this amounts to building a totally random decision tree.
#### 3) Bernoulli Naïve Bayes
In machine learning, Naïve Bayes classifiers are a family of simple "probabilistic classifiers" based on applying Bayes' theorem with strong (naive) independence assumptions between the features. The Bernoulli Naïve Bayes classifier assumes that all features are binary such that they take only two values (e.g. a nominal categorical feature that has been one-hot encoded).
#### 4) LightGBM
A fast, distributed, high performance gradient boosting (GBT, GBDT, GBRT, GBM or MART) framework based on decision tree algorithms, used for ranking, classification and many other machine learning tasks. Project of Microsoft.
#### 5) CatBoost
CatBoost is an algorithm for gradient boosting on decision trees. It is developed by Yandex, and is for search, recommendation systems, personal assistance, self-driving cars, weather prediction...

## Implementation

### Preprocessing and feature engeneering
Dataset contains many columns with over a million null values. Those columns were removed as well as columns containing police codes. Spatial data contained many values that were not in bounding box of NYC and those were removes as well. Only dates after 2006 were used since there are many inconsistencies in data before. Highest quality data recording is the one after 2011 but i decided to include the dates before since i wanted to observe the crime trends. Crime classes that were ambiguously defined e.g. 'Other state laws' or 'Administrative laws' were removed as well as very rare crimes. Crimes that could be classified as an existing higher count crime were added to that class. Feature engeneering consisted of addind columns such as minute, hour, day, day of the week, month, year and days since begining of crime recording. Crimes were encoded to values between 0 and 35. Column n_days was normalized. Longitudes and latitudes were transformed as three dimensional represetation as advised [here](https://datascience.stackexchange.com/questions/13567/ways-to-deal-with-longitude-latitude-feature). Ciclical data such as months, days, hours, minutes i represended as ordinal projections. 

### EDA
Here i analyzed the crime trends, spatial distribution of crime as well as time of day that they were commited. Results can be view in images folder.

### Prediction
Algoriths used for prediction were Random Forest, Extra Tree Classifier, Bernoulli Naïve Bayes, LightGBM and Catboost. Base models and models with tuned parameteres were compared. Best performed model was LightGBM with cross-entropy log-loss of 2.3204.

## Evaluation Metric
Models are evaluated using the multi-class logarithmic loss. Each incident has been labeled with one true class. For each incident, you must submit a set of predicted probabilities (one for every class). The formula is:

![alt text](http://www.sciweavers.org/upload/Tex2Img_1562236240/render.png)

where N is the number of cases in the test set, M is the number of class labels, log is the natural logarithm, ![alt text](http://www.sciweavers.org/upload/Tex2Img_1561670812/render.png) is 1 if observation i is in class j and 0 otherwise, and ![alt text](http://www.sciweavers.org/upload/Tex2Img_1561670854/render.png) is the predicted probability that observation i belongs to class j.

## Results

### Base models

| Algorithms | Parameters | Logloss |
| - | - | - |
| Random Forest | Default Scikit-Learn Parameters | 15.92259 |
| Extra tree classifier | Default Scikit-Learn Parameters | 17.99393 |
| BernoulliNB | Default Scikit-Learn Parameters | 2.66542 |
| LightGBM | Default Scikit-Learn Parameters | 8.17752 |
| Catboost | Default Scikit-Learn Parameters | / |

### After parameter tuning

| Algorithms | Parameters | Logloss |
| - | - | - |
| Random Forest | Custom Parameters | 2.65289 |
| Extra tree classifier | Custom Parameters | 2.60101 |
| BernoulliNB | Custom Parameters | 2.66542 |
| LightGBM | Custom Parameters | 2.31863 |
| Catboost | Custom Parameters | 2.52019 |


## Improvements

We are sure that there is a lot of space for improvement. Two additional techniques we would like to implement if there was the necessary time would be:

- Additional parameter tuning.

- Introduce aditional data that shows economic, educational, and other socio-economic information for each borough. That way algorithms could notice and exploit patterns asociated with these factors and provide even better score. 

- Use embeddings or other processing techniques for the addresses. For example we could extract if an incident has happened in a block or a crossroad or a boulding etc. There are definitely some additional correlations between incident place and crime commited.

- Use neural nets and combine the prediction with results from lgbm.
