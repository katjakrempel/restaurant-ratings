# Predicting Yelp and food hygiene ratings based on restaurant characteristics

### Objective

The motivation for this project was twofold. First, I was interested in whether it is possible to predict a restaurant’s food hygiene rating based on characteristics such as location, price range or cuisine. From a local authority’s point of view, identifying non-compliant businesses could help with prioritisation of on-site inspections. Second, I was taking a business perspective and wanted to understand which restaurant characteristics determined high Yelp ratings. Which attributes mattered to customers?

### Contents

Data Collection
Data Cleaning
Feature Engineering
Exploratory Data Analysis
Modelling
Limitations
Conclusions
Future Work
Key Learnings
Contact

### Data Collection

The data sets were compiled from two sources. I used the Food Standards Agency API to collect the most recent food hygiene ratings of over 224,000 restaurants and the Yelp API to obtain ratings and attributes of over 55,000 restaurants across 20 large UK cities.

In order to join the two data sets I created a unique identifier from restaurant name and postcode. This required some preliminary data cleaning to account for different spelling of restaurant names.

The consolidated data set contained 15,725 observations.

### Data Cleaning

The following variables required more extensive data cleaning and processing:

- All businesses without a numerical food hygiene rating (status “awaiting inspection”, “awaiting publication” or “exempt”) were removed from the data set. 
- The Food Hygiene Rating Scheme was launched in 2010. Therefore observations with a food hygiene rating date before 2010 were dropped.
- The main category (cat_1) of over 170 businesses was not related to food preparation (for example zoos or swimming pools). These rows were removed, as were 2 rows with missing values.

After cleaning the data, there were 15,126 remaining observations.

Note: The price variable had nearly 40 percent missing values. All models using price level as a predictor were run on a subset of the data containing 9,160 observations.

### Feature Engineering

The following features were created from the underlying raw data:

- is_chain: restaurants that belong to a chain were identified by reviewing the 250 most common business names in the data set (49 restaurant chains identified in total).
- postc_area: the “city” variable contained almost 1,000 unique values of varying granularity (for example “Kensington” and “London”). In order to avoid manually cleaning the values I extracted the postcode area from the postcode and used it as a proxy for restaurant location. 

### Exploratory Data Analysis

The EDA process revealed that class imbalance was present in both potential target variables. This affected in particular the food hygiene rating variable, with only 4% of observations having a rating below 3. 

![Food Hygiene Ratings](/assets/images/food-hygiene-ratings.png)

In terms of location, the data set is skewed towards restaurants in London as can be seen from the graphs below (30 percent of restaurants in the data set are based in London).

Restaurants belonging to a chain made up 12 percent of the sample (this might be underestimating the true percentage, as pointed out under “Limitations”).

The review count variable is highly skewed, with a median of 3 Yelp reviews per restaurant and over 200 reviews for the top 30 restaurants. Restaurants with lower ratings seem to have fewer reviews.

The heatmap shows that correlations were unfortunately quite low (except for food hygiene rating and its component scores, however these features are not independent).

### Modelling

#### Binary Classification

Several binary classification models were run in order to predict if a restaurant's food hygiene rating or Yelp rating were high. The cut-off point used was 5 for a high food hygiene rating and 4 for a high Yelp rating.

Both a Logistic Regression and a Decision Tree Classifier were applied to the full dataset and to a subset of the data for which price information was available. 

The best performing model was a Logistic Regression applied to the data subset, which predicted a high Yelp rating. The mean cross-validated accuracy score was 0.6258, which is 4.1 percentage points above the baseline. Recall was 0.42 for the low Yelp rating class, i.e. only 42% of truly low Yelp ratings were correctly predicted as such. Precision was 0.66 for the high Yelp rating class and 0.61 for the low Yelp rating class - 66% of observations predicted as high and 61% of observations predicted as low were correctly labelled. Overall, the model showed limited predictive power.

Review count had the highest positive coefficient, i.e. all else being equal a high number of reviews increased the probability of the Yelp rating being classed as high the most. The strongest decrease in the probability of a high Yelp rating resulted from the restaurant being a pub or part of a chain.

#### Multiclass Classification

Multiclass classification models using a Decision Tree Classifier were applied to the data subset for which price information was available. The aim was to predict specific food hygiene and Yelp ratings rather than a high or low rating.

The accuracy was close to the baseline as the models predicted the majority class (food hygiene rating of 5 and Yelp rating of 4) but failed to classify the minority classes correctly. Recall was very low for the minority classes and in some cases close or equal to 0. Overall, the multiclass classification models had poor predictive power.

The SMOTE algorithm was applied to address the underlying problem of class imbalance. Recall was improved by using SMOTE, however this applied mainly to the training scores and not the test scores. This indicates overfitting and means the model doesn't generalise well on unseen data.

### Limitations

- Data Set: The final data set resulted from matching restaurants in two separate data sets (Yelp and Food Standards Agency data). This considerably reduced the number of observations which could be used for modelling. It also might have introduced bias as the resulting subset might not be representative of the overall restaurant population.
- Predictors: The limited availability of restaurant attributes through the Yelp API limited the number of predictors which could be used for modelling. There might also be bias due to the approach for identifying restaurants belonging to chains (possibly underestimating the number of chain restaurants).
- Class Imbalance: The main limitation of the models stems from an underlying imbalance of classes in the data set, which could not be fully resolved by applying the SMOTE algorithm.

### Conclusions

The models give some insights into which restaurant characteristics mattered most for a high Yelp rating. Review count in particular seems to increase the probability of the Yelp rating being high. A caveat regarding the direction of this relationship is that a high rating might drive the number of reviews rather than the reverse. The greatest decrease in the probability of a high Yelp rating resulted from the restaurant being a pub or part of a chain. The latter could be due to the fact that most chain restaurants in the sample belonged to fast food chains, a category which also had one of the lowest coefficients.

### Future Work

I would like to take this work further by exploring some of the ideas listed below:

- NLP: Obtain Yelp reviews for the restaurants in the data set and use NLP to improve the predictive power of the models.
- Further feature engineering: Improve methodology for identifying chain restaurants and create new features from the raw data. 
- Modelling: I have focused on Logistic Regression and Decision Tree Classifier for this project. Model scores could potentially be improved by using different classifiers.

### Key Learnings

Apart from going through the steps of the data science workflow end-to-end, this project has taught me to be persistent and find solutions when things don’t go according to plan. My main challenge was to join the two data sets I had compiled. First attempts using the restaurants’ geolocation (longitude and latitude) did not provide the accurate matches I had expected. I then used a combination of postcode and restaurant name as a workaround, which allowed me to match a sufficiently large number of observations to proceed with EDA and modelling.

### Contact

If you found this project interesting (or have suggestions for improvement), please feel free to reach out on [LinkedIn](https://www.linkedin.com/in/katjakrempel/).


