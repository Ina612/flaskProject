<h1> :octocat: Yandex Realty Real Estate Price Predictions </h2>

The [dataset](https://github.com/olgaselesnjova/E2E/blob/main/spb.real.estate.archive.sample5000.tsv) is from [Yandex.Realty](https://realty.yandex.ru)

<h3> :one: Dataset description: </h3>

Real estate listings from Yandex Realty service of 2016 - 2018. 

<h3> :two: EDA and data preparations: </h3>

1. Price median and mean for sell and rent apartments in St. Petersburg.
2. Removed outliers: too cheap or expensive apartments. 
3. The most cheapest and most expensive apartments per square meter.
4. How many rent offers have the commission and the most popular commission.

File [EDA_real_estate_data.ipynb](https://github.com/olgaselesnjova/E2E/blob/main/EDA_real_estate_data.ipynb) contains the EDA of the dataset.

<h3> :three: Pre-psocessing </h3>

**SimpleImputer** with sklearn's "mean" to replace missing values by the mean value.
**OneHotEncoder** to convert categorical variables into a format for ML algorithms.

```
mapper = DataFrameMapper([([feature], SimpleImputer()) for feature in numeric_features] +\
                         [([feature], OneHotEncoder(handle_unknown = 'ignore')) for feature in nominal_features], 
                            df_out=True)  
```		

<h3> :four: ML </h3>

The data processing and model building process is automated with **pipeline** to increase the efficiency, accuracy and reproducibility of models.

```
xgboost_pipeline = Pipeline(steps = [('preprocessing', mapper), 
                             ('scaler', StandardScaler()),
                             ('xgb', xgb.XGBRegressor(objective="reg:linear", random_state=42))])
```			     
The models used on the data with their accuracy: 

    - XGBRegressor = 63.65%
    - CatBoostRegressor = 62.23%

The best result provides **XGBRegressor** with the following parameters:

```
param_grid = dict(xgb__learning_rate = [0.1], xgb__n_estimators = [100], xgb__max_depth = [5])
xgboost = GridSearchCV(estimator=xgboost_pipeline, param_grid=param_grid, cv=3, n_jobs=2, verbose=2)
```
 
<h3> :five: Run the web-app with virtual environment </h3>
	
<h4> 🌙 Dockerfile </h4>

The **Dockerfile** runs on Ubuntu:20.04 image. The MAINTAINER command sets the author information for the image. 
- RUN command - to update the package list on the Ubuntu image. 
- COPY - to copy the content of the current directory to the /opt/gsom_predictor directory inside the Docker container. 
- WORKDIR - to set the working directory inside the container as /opt/gsom_predictor. 
- RUN - to install the pip3 package manager for Python3. 
- RUN - to set the dependencies listed in requirements.txt file. 
- CMD - to run the app.py file inside the container.
	
<h4> 🍀 Open the port in a VM </h4>
	
Specify the port in an app in the script **app.py** by setting 
```
if __name__ == '__main__':
    app.run(debug = True, port = 5444, host = '0.0.0.0')
```
To open the remote VM port of our web application, use the chunks:
```
sudo apt install ufw
sudo ufw allow 5444 
```
Use **Postman** to check how the API requests work.  

<h4> 🐈‍⬛ Run the app with docker </h4>

Build containers and then run: 
```
docker build -t <your login>/<directory name>:<version>
docker run --network host -it <your login>/<directory name>:<version> /bin/bash
docker run --network host -d <your login>/<directory name>:<version>   
docker ps   
docker stop <container name>  
```
