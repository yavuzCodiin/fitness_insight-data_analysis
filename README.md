# <ins>fitness_insight-data_analysis</ins>
Exploring/analyzing fitness trends to identify product niches with pandas and matplotlib

## <ins>Importing libraries</ins>
```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
sns.set(style='white', palette='husl')
import os
```

## <ins>Creating Load Functions</ins>
```python
def read_file(filepath, plot = True):
    file = pd.read_csv(filepath, header=1)
    df = file.set_index('Week').stack().reset_index()
    df.columns = ['week','region','interest']
    df['week'] = pd.to_datetime(df['week'])
    plt.figure(figsize=(8,3))
    df = df[df['interest']!="<1"]
    df['interest'] = df['interest'].astype(float)

    if plot:
        sns.lineplot(data = df, x= 'week', y= 'interest',hue='region')
    return df
```
```python
def read_geo(filepath, multi=False):
    file = pd.read_csv(filepath, header=1)

    if not multi:
        file.columns = ['country', 'interest']
        plt.figure(figsize=(8,4))
        sns.barplot(data = file.dropna().iloc[:25,:], y = 'country', x='interest')

    if multi:
        plt.figure(figsize=(3,8))
        file = file.set_index('Country').stack().reset_index()
        file.columns = ['country','category','interest']
        file['interest'] = pd.to_numeric(file['interest'].apply(lambda x: x[:-1]))
        sns.barplot(data=file.dropna(), y = 'country', x='interest', hue='category')

    file = file.sort_values(ascending=False,by='interest')
    return file
```

## <ins>Loading and first look at the CSV file</ins>
```python
workout = read_file('data/workout.csv')
workout
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/04c07f70-8270-470f-bac7-43dbbabd5dfb)

## <ins>Global interest in fitness</ins>
```python
workout_by_month = workout.set_index('week').resample('MS').mean()
workout_by_month
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/8ddd02da-f1ff-41a1-a5a9-9518be6576ca)

```python
month_high = workout_by_month[workout_by_month['interest']==workout_by_month['interest'].max()]
month_high
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/2a66f426-1382-49cc-b481-0eaebaa197eb)

## <ins>Comparison of interest in home workouts, gym workouts and home gyms</ins>

```python
workout = read_file('data/three_keywords.csv')
current = 'gym workout'
peak_covid = 'home workout'
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/f26ba760-45ab-4b97-a3e7-592fe035a5c4)

## <ins>Segmentation of global interest by region</ins>

```python
workout_global = read_geo('data/workout_global.csv')
workout_global
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/71f8f177-ca7e-4d45-8c29-51a6a2dc4eb3)

```python
top_25_countries = workout_global.head(25)
top_country = top_25_countries['country'].iloc[0] # => "United States"
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/2f888426-ffa9-4927-a96b-85e449b60035)

## <ins>Regional demand for home workouts, gym workouts and home gyms</ins>

```python
geo_categories = read_geo('data/geo_three_keywords.csv', multi=True)
geo_categories
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/f6238eb2-fd09-437b-8e80-18eaf8ddaace)

```python
MESA_countries = ["Philippines", "Singapore", "United Arab Emirates", "Qatar", "Kuwait", "Malaysia", "Sri Lanka", "India", "Pakistan", "Lebanon"]
MESA = geo_categories.loc[geo_categories.country.isin(MESA_countries), :]
MESA
```

![image](https://github.com/yavuzCodiin/fitness_insight-data_analysis/assets/82445309/018c370c-2daf-4a21-a96b-afda5d2bc699)




