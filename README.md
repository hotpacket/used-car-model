# used-car-model
Practical Exercise II for AI/ML Class

## What Drives Used Cars Prices?

To answer this question, I created an ML model using the Kaggle used car sales dataset provided in class.

## Data Cleanup

This dataset was quite messy, and needed thorough cleaning. My approach to cleaning the dataset included the following:

1. Removal of the following columns:
- VIN - not relevant to the price
- Size - large number of null values, and size information will be captured by other features including vehicle type and number of cylinders
- Model - with over 29,000 unique values in the data for model, this information is unwieldy for our model.
- Region - there are 404 unique regions in the database. For efficiency reasons, we decided to set aside region, with the intent to include it if the state turns out to be important.
2. One-hot encoding for the remaining categorical columns, including manufacturer and fuel type.
  
3. Removal of errors/outliers. A large number of vehicles had a listed price of zero, and several had a listed price of over a billion dollars. Likewise, some vehicles had a listed odometer reading of 10 million miles. Some vehicles appeared to erroneously be assigned years between 1900 and 1923. All entries with prices under $100, prices over $1,000,000, years before 1924, and  odometer readings over 400,000 miles were eliminated from the database.
	
The data was scaled with StandardScaler, and then divided into training and test datasets using simple cross validation.

## Modeling

After removal of 4 columns, and implementing one-hot encoding, I was left with a dataset of  151 columns. I tried linear regresssion, sequential feature selection, Ridge regression, and Lasso regression, and found very high mean squared error in every case. Polynomial features were impossible due to the high number of columns and my limited computing power.

The lowest MSE at this stage was with simple linear regression:

Linear Training MSE:  103768092.5481182

Linear Test MSE:  103133732.76988871

Next, I decided to eliminate all but the most important features, and then attempt polynomial features on those. To determine the most important features, I examined my results with Lasso regression, single feature selection, and a correlation matrix focused on price. All three mechanisms delivered similar results. I made a list of all features that showed up in at least two of the three feature lists and came up with these:

1. odometer
2. cylinders_4ylinders
3. cylinders_8 cylinders
4. drive_fwd
5. drive_4wd
6. fuel_gas
7. fuel_diesel
8. type_sedan
9. type_pickup
10. type_truck
11. Year

I then performed a linear regression on these 11 features with polynomial features of degree two. This performed far, far better.

Polynomial Features (top 11) Training MSE:  0.4147361827707379

Polynomial Features (top 11) Test MSE:  0.40257850379721155

I next tried multiple model types with the 11 polynomial features, and got a slightly more optimal model with Ridge regression (with alpha equal to 100):

Polynomial Features with Lasso Training MSE:  0.41473631813720796

Polynomial Features with Lasso Test MSE:  0.4025783765245659

## Evaluation

Using the select model, I used the Lasso coefficients to rank the most important features affecting the price of a used car. The following list ranks the features in order of importance. I added a "star rating," to give a sense of the importance of each feature on the price.

1. Year - 5 stars - The vehicle year has an strong and nonlinear relationship with price, see chart below
2. Odometer - 3 stars - Unsuprisingly, lower mileage is more expensive
3. Number of Cylinders - 2 stars - 8 is most expensive, 4 is least expensive
4. Fuel type - 1.5 stars - Vehicles with diesel engines are most expensive, followed by other (hybrid), followed by regular gasoline
5. Drive type - 1.5 stars - 4 wheel drive is most expensive, front wheel drive is least expensive
6. Vehicle type - 0.5 stars - Truck/pickup is most expensive, sedan is least expensive

Other features, including condition, state, paint color, and manufacturer had a much smaller impact on the price. 

The year had a nonlinear relationship to price, as seen in the plot below.

<img width="685" alt="image" src="https://github.com/hotpacket/used-car-model/assets/136177819/1af6da11-33a4-446c-adc7-ba4b9b5d3db4">

There is increased value in the early 1950s through the early 1970s. From around 1973 through 2000 the price stays relatively low and flat, and then steadily increases between 2000 and 2022.



