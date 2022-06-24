# King County Sales

Flatiron School Phase 2 Project

Harrison Carter

Flatiron School NYC Data Science cohort 05162022

![alt text](https://www.wta.org/site_images/hikes/dsc_0030.jpg/@@images/11f0a298-5a81-4457-963a-19417224d1d6.jpeg)

## Project Description

The goal of this project is to use the dataset provided by King County, WA to make predictions and inferences about housing prices according to potential predictors therein. The focal point is to use multiple linear regression to explore the parameters that correlate with my target variable (sale price) in order to build an acceptable model for analysis. All of the computational work here is done in Python and associated libraries (primarily sklearn). The primary predictor here for price is the Zip code for the associated residence, secondary predictors being the square footage of the associated living space, its waterfront juxtaposition or lack thereof, and ZIP code.

## Methodology

First get the data.

`df = pd.read_csv('data/kc_house_data.csv')`

To display the heatmap where we establish a baseline model and inspect the correlation matrix between each variable to help determine what we want to focus on in regards to pricing, use the following:

`plt.figure(figsize=(14, 6))

sns.heatmap(df.corr(), annot=True)

plt.title('Correlation Matrix for Features')

plt.show()`

The following code prepares the dataframe for analysis by eliminating NA values, taking the log of both price and sqft_living columns, and creates dummy data for two categorical variables:
`df['l_price'] = np.log(df['price'])
df['sqft_living_trans'] = np.log(df['sqft_living'])
df_clean = df.dropna(axis = 0, how = 'any')
df_dum = pd.get_dummies(df_clean, columns = ['waterfront', 'zipcode'], drop_first = True)`

This prepares our first model and displays its regression results for inspection of the initial model between ZIP code and log price:
`X1, y1 = df_dum.drop(['id', 'date', 'price', 'bedrooms', 'bathrooms', 'sqft_living',
       'sqft_lot', 'floors', 'view', 'condition', 'grade', 'sqft_above',
       'sqft_basement', 'yr_built', 'yr_renovated', 'lat', 'long',
       'sqft_living15', 'sqft_lot15', 'l_price', 'grade_final', 'sqft_living_trans',
       'waterfront_YES'], axis=1), df_dum['l_price']
X1 = sm.add_constant(X1)
baseline = sm.OLS(y1, X1).fit()
baseline.summary()`
By comparing ZIP code, we already account for around half of the variance in the data with the r squared value of 0.533. Rule of thumb is that the skew should be less than +/-0.5 for proper regression and the kurtosis should not exceed 6 to ensure normality. Here, skew is too high at 0.594 and kurtosis is borderline, with a value of 5.132. We can fix this going forward.

The following code establishes our final model and displays the regression results:
`X, y = df_dum.drop(['id', 'date', 'price', 'bedrooms', 'bathrooms',
       'sqft_lot', 'floors', 'view', 'condition', 'grade', 'sqft_above',
       'sqft_basement', 'yr_built', 'yr_renovated', 'lat', 'long',
       'sqft_living15', 'sqft_lot15', 'l_price',
            'sqft_living', 'grade_final'
       ], axis=1), df_dum['l_price']
X = sm.add_constant(X)
model1 = sm.OLS(y, X).fit()
model1.summary()`
I selected the living area to incorporate into the multiple regression to boost the r squared value, indicating a much better fit and capturing much more variance. Using the log of the square footage for the living area does not change the r squared, but does eliminate some kurtosis and much of the skew. Conversely, incorporating the availability of waterfronts 