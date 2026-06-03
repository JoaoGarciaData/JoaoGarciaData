# Case Study 3: Lisboa and Porto Airbnb Listings


## 💾 Contents
- [Task](#-task)
- [Wrangling](#-wrangling)
- [Analysis](#-analysis)



## 📚 Task
● Clean the data

● Fix the amenities column
    
● Exploratory data analysis

● Create dataframes for visualization




## 🧹 Wrangling

<br>

### 🌉 PORTO

<br> 
●  Standerdize the amenities column to lowercase
 
```python
dadosporto['amenities']= dadosporto['amenities'].str.lower()
```


● Regex-based lookup dictionary to group unique unstructured amenities into fewer, standardized columns

```python
columns = {
    '[^"]*hot tub[^"]*' : "Hot Tub",
    '[^"]*refrigerator[^"]*' :"Refrigerator",
    '[^"]*tv[^"]*' : "TV",
    '[^"]*(oven|stove|freezer|microwave|toaster|cooking|kitchen|utensils|coffee|baking sheet|blender|dishes|kettle)[^"]*' :"Kitchen Suplies",
    '[^"]*(ductless|conditioning|unit)[^"]*' :"Air Conditioning",
    '[^"]*grill[^"]*' :"Grill",
    '[^"]*backyard[^"]*' :"Backyard",
    '[^"]*beach[^"]*' :"Beach in the proximity",
    '[^"]*(parking|garage)[^"]*' :"Parking",
    '[^"]*(gym|exercise)[^"]*' :"Exercice/Gym", 
    '[^"]*breakfast[^"]*' : "Breakfast",
    '[^"]*(shampoo|conditioner)[^"]*' :"Hair Products",
    '[^"]*carbon[^"]*' :"Carbon Monoxide Alarm",
    '[^"]*dishwasher[^"]*' :"Dishwasher",
    '[^"]*(washer|dryer|iron|ironing)[^"]*' :"Laundry",
    '[^"]*charger[^"]*' : "Ev Charger",
    '[^"]*extinguisher[^"]*' :"Fire Extinguisher",
    '[^"]*game[^"]*' :"Games",
    '[^"]*(housekeeping|stay)[^"]*' :"Housekeeping",
    '[^"]*fireplace[^"]*' :"Fireplace",
    '[^"]*pets[^"]*' :"Allowed Pets",
    '[^"]*pool[^"]*' : "Pool",
    '[^"]*(soap|gel)[^"]*' : "Soap",
    '[^"]*sound[^"]*' : "Sound Sistem",
    '[^"]*(clothing|closet|hangers)[^"]*' :"Clothing Storage",
    '[^"]*wifi[^"]*' : "Wifi",
    '[^"]*(heated|heating)[^"]*' : "Heating",
    '[^"]*(high|baby|children|changing|crib)[^"]*' : "Fit for babies/children",
    '[^"]*sauna[^"]*' : "Sauna"
}
```

● Looping trough every column, replacing the phrase/word where the key is present, to the value 

```python
for k, v in columns.items():
   dadosporto["amenities"] = dadosporto["amenities"].str.replace(k, v, regex=True)
```


● Removing unwanted ponctuation

```python
dadosporto['amenities'] = dadosporto['amenities'].str.replace({'[': '', '"' : '', ']': ''})
```


● From the amenities column, creates a data frame with each unique value (separated by comma) being a column

```python
amenities_grid = dadosporto['amenities'].str.get_dummies(sep=', ')
```



● Deletes any column where there are less than 1000 true entries

```python
amenities_grid = amenities_grid.loc[:, amenities_grid.sum() >= 1000]
```



● changes the 1 and 0 to Yes and No for better visualization

```python
amenities_grid = amenities_grid.replace({1: 'Yes', 0: 'No'})
```

● Creates the new amenities table

```python
amenities_grid.to_csv('amenitiespor.csv',index=False)
## after this, i deleted some columns that didnt add anything to the analysis, the code above will give more columns than there are in the final table
```

● Joining the Listings table with the new amenities table

```python
dadosporto = pd.concat([dadosporto, amenitiespor], axis= 1)
```
___________________________________________________________________
<br>

### 🚃 LISBOA
<br>

● Arrays with words with typos and the correct counterpart
```python
wrong = [ 
    'Alcntara', 'Estvo', 'Algs',
    'Joo', 'Pro', 'Arranh', 'Belm', 'Cacm', 'Apelao',
    'Cabea', 'Carmes', 'Encarnao', 'Rosrio', 'So ', 'Lourinh',
    'Maussa', 'Massam', 'Abrao', 'gua' , 'Misericrdia',
    'Pao ', 'Naes', 'Frana', 'Fames', 'Pvoa', 'Caneas',
    'Mataces', 'Sacavm', 'Antnio', 'Agrao', 'Gals', 'guas '
]


right = [
    'Alcântara ', 'Estêvão ', 'Algés ', 'João ', 'Pêro ', 'Arranhó ', 'Belém ',
    'Cacém ', 'Apelação ', 'Cabeça ', 'Carmões ', 'Encarnação ', 'Rosário ', 'São ',
    'Lourinhã ', 'Maçussa ', 'Massamá ', 'Abraão ', 'Água ', 'Misericórdia ',
    'Paço ', 'Nações ', 'França ', 'Famões ', 'Póvoa ', 'Caneças ', 'Matacães ',
    'Sacavém ', 'António ', 'Agraço ', 'Galés ', 'Águas '
]
```


● Fixing the typos on the names since some are missing a character, any accented characters were deleted when gathering the data 

```python
dadoslisboa['neighbourhood_cleansed'] = dadoslisboa['neighbourhood_cleansed'].replace(
    to_replace=wrong,
    value=right,
    regex=True)

dadoslisboa['neighbourhood_group_cleansed'] = dadoslisboa['neighbourhood_group_cleansed'].replace({
    'Lourinh' : 'Lourinhã ', 
    'Agrao' : 'Agraço '
}, regex=True)
```

● removing any unwanted spaces

```python
dadoslisboa['neighbourhood_cleansed'] = dadoslisboa['neighbourhood_cleansed'].str.replace(r' +', ' ', regex=True)

text_cols = dadoslisboa.select_dtypes(include= ['object', 'category']).columns
dadoslisboa[text_cols] = dadoslisboa[text_cols].apply(lambda x: x.str.strip())
```


●  Standerdize the amenities column to lowercase

```python
dadoslisboa['amenities']= dadoslisboa['amenities'].str.lower()
```


● Regex-based lookup dictionary to group unique unstructured amenities into fewer, standardized columns

```python
columns = {
    '[^"]*hot tub[^"]*' : "Hot Tub",
    '[^"]*refrigerator[^"]*' :"Refrigerator",
    '[^"]*tv[^"]*' : "TV",
    '[^"]*(oven|stove|freezer|microwave|toaster|cooking|kitchen|utensils|coffee|baking sheet|blender|dishes|kettle)[^"]*' :"Kitchen Suplies",
    '[^"]*(ductless|conditioning|unit)[^"]*' :"Air Conditioning",
    '[^"]*grill[^"]*' :"Grill",
    '[^"]*backyard[^"]*' :"Backyard",
    '[^"]*beach[^"]*' :"Beach in the proximity",
    '[^"]*(parking|garage)[^"]*' :"Parking",
    '[^"]*(gym|exercise)[^"]*' :"Exercice/Gym", 
    '[^"]*breakfast[^"]*' : "Breakfast",
    '[^"]*(shampoo|conditioner)[^"]*' :"Hair Products",
    '[^"]*carbon[^"]*' :"Carbon Monoxide Alarm",
    '[^"]*dishwasher[^"]*' :"Dishwasher",
    '[^"]*(washer|dryer|iron|ironing)[^"]*' :"Laundry",
    '[^"]*charger[^"]*' : "Ev Charger",
    '[^"]*extinguisher[^"]*' :"Fire Extinguisher",
    '[^"]*game[^"]*' :"Games",
    '[^"]*(housekeeping|stay)[^"]*' :"Housekeeping",
    '[^"]*fireplace[^"]*' :"Fireplace",
    '[^"]*pets[^"]*' :"Allowed Pets",
    '[^"]*pool[^"]*' : "Pool",
    '[^"]*(soap|gel)[^"]*' : "Soap",
    '[^"]*sound[^"]*' : "Sound Sistem",
    '[^"]*(clothing|closet|hangers)[^"]*' :"Clothing Storage",
    '[^"]*wifi[^"]*' : "Wifi",
    '[^"]*(heated|heating)[^"]*' : "Heating",
    '[^"]*(high|baby|children|changing|crib)[^"]*' : "Fit for babies/children",
    '[^"]*sauna[^"]*' : "Sauna"
}
```
● Looping trough every column, replacing the phrase/word where the key is present, to the value 

```python
for k, v in columns.items():
   dadoslisboa["amenities"] = dadoslisboa["amenities"].str.replace(k, v, regex=True)
```


● Removing unwanted ponctuation

```python
dadoslisboa['amenities'] = dadoslisboa['amenities'].str.replace({'[': '', '"' : '', ']': ''})
```


● From the amenities column, creates a data frame with each unique value (separated by comma) being a column

```python
amenitieslis = dadoslisboa['amenities'].str.get_dummies(sep=', ')
```


● Deletes any column where there are less than 1000 true entries****
```python
amenitieslis = amenitieslis.loc[:, amenitieslis.sum() >= 1000]
```

● Creates the new amenities table

```python
## changes the 1 and 0 to Yes and No for better visualization
amenitieslis = amenitieslis.replace({1: 'Yes', 0: 'No'})
```
● Creates the new amenities table

```python
amenitieslis.to_csv('amenitieslis.csv', index= False)
```

● Joining the Listings table with the new amenities table
```python
dadoslisboa = pd.concat([dadoslisboa, amenitieslis], axis= 1)
```



## 🔍 Analysis

<br>

### 🌉 PORTO
<br>

● Count of listings per neighbourhood
```python
Bairros = porto.groupby('neighbourhood_group_cleansed')['neighbourhood_group_cleansed'].count()
```

● Count of listings per property type

```python
Propriedade = porto.groupby('property_type')['property_type'].count()
```

● Yearly number of LAST reviews

```python
porto['last_review']= pd.to_datetime(porto['last_review'], errors='coerce', format='mixed')

Ativos= porto.groupby(pd.Grouper(key='last_review', freq= 'YE')).size()
```


● Amenities present per neigbourhood

```python
com_cols = [
    "Refrigerator", "TV", "Kitchen Suplies", "Air Conditioning", "Grill",
    "Beach in the proximity", "Parking", "Carbon Monoxide Alarm",
    "Laundry", "Fire Extinguisher", "Housekeeping", "Allowed Pets",
     "Pool", "Clothing Storage", "Wifi", "Heating"
] 

Comodidades = porto.groupby('neighbourhood_group_cleansed')[com_cols].apply(lambda x: (x == 'Yes').sum())
```

● Some metrics 
```python
Accommodates = porto['accommodates'].describe().round()

Ratings = porto[['review_scores_rating','review_scores_cleanliness', 'review_scores_checkin', 'review_scores_communication', 'review_scores_location']].describe().round(2)
```


● Creates the data frames to turn into visualizations
```python
Accommodates.to_csv('Accommodates.csv')
Ratings.to_csv('Ratings.csv')
Comodidades.to_csv('Comodidades.csv')
Ativos.to_csv('Ativos.csv')
Propriedade.to_csv('Propriedade.csv')
Bairros.to_csv('Bairros.csv')
```
___________________________________________________________________
<br>

### 🚃 LISBOA
<br>

● Count of listings per neighbourhood

```python
Bairros = lisboa.groupby('neighbourhood_group_cleansed')['neighbourhood_group_cleansed'].count()
```

● Count of listings per property type

```python
Propriedade = lisboa.groupby('property_type')['property_type'].count()
```

● Yearly number of LAST reviews

```python
lisboa['last_review']= pd.to_datetime(lisboa['last_review'], errors='coerce', format='mixed')

Ativos= lisboa.groupby(pd.Grouper(key='last_review', freq= 'YE')).size()
```

● Amenities present per neigbourhood

```python
com_cols = [
    "Refrigerator", "TV", "Kitchen Suplies", "Air Conditioning", "Grill",
    "Beach in the proximity", "Parking", "Carbon Monoxide Alarm",
    "Laundry", "Fire Extinguisher", "Games", "Housekeeping", "Allowed Pets",
    "Pool", "Clothing Storage", "Wifi", "Heating"
] 

Comodidades = lisboa.groupby('neighbourhood_group_cleansed')[com_cols].apply(lambda x: (x == 'Yes').sum())
```

● Some metrics

```python
Accommodates = lisboa['accommodates'].describe().round()

Ratings = lisboa[['review_scores_rating','review_scores_cleanliness', 'review_scores_checkin', 'review_scores_communication', 'review_scores_location']].describe().round(2)
```

● Creates the data frames to turn into visualizations

```python
Bairros.to_csv('Bairros.csv')
Propriedade.to_csv('Propriedade.csv')
Comodidades.to_csv('Comodidades.csv')
Accommodates.to_csv('Accommodates.csv')
Ratings.to_csv('Ratings.csv')
Ativos.to_csv('Ativos.csv')
```


































