# ETL Pipeline with python

## Background

Stannness bikez is a fictitious bicycle hailing company poised  to revolutionalize urban mobility. With the increase need of sustainable and convenient means of transportation options in the urban areas, Stanness Bikez offers a refreshing solution the combines technology, eco-friendiliness and health benefits. Stanness Bikez being company who operations are heavily impacted by atmosheric and weather conditions, there is the need for constant weather forecasting and informations for smooth service delivery.

## why do Stanness Bikez need weather data?

1. To provide real-time alert to user of the service on unsafe areas and possibly restrict service to weather conditions
2. To adjust pricing base on weather condition.
3. To predict what time and location  where demand of the service will be needed most
4. To schedule maintainance of fleet.

![1717530309143](image/README/1717530309143.png)

## Overview of the project

This project involves Extracting, Transforming, and Loading (ETL) data from the OpenWeather API into a PostgreSQL database on behalf of Stanness Bikez. The extracted weather data will be used for various analytical purposes within the company's operations.

## Project Summary

The project was performed in jupyter notebook. the version of used was python 3.11. weather data was access from the open weather api through an api key(exposed key deleted prior to publishing of this project). transformed data was loaded to an on premise postrgesql database for further analysis

1. Data was extracted using the request library.

```bash
city_name = "Accra,GH"

url = f"http://api.openweathermap.org/data/2.5/forecast?q={city_name}&appid={API_key}"
response = requests.get(url)
```

2. Extracted data was formated as a json file

   ```

   data = response.json()
   json_xtrct = json.dumps(data, indent = 4)
   print(json_xtrct)
   ```
3. transforming the extracted json file from kelvin to degree celsius using a function

   ```
   def kelvin_to_celsius(temp_k):
       return temp_k - 273.15
   ```
4. Further transformation from json file to a pandas dataframe

```
if response.status_code == 200:
    data = response.json()
    weather_data = []
    for hour in data['list']:
        date_time = datetime.utcfromtimestamp(hour['dt']).strftime('%Y-%m-%d %H:%S')
        temp = kelvin_to_celsius(hour['main']['temp'])
        feels_like = kelvin_to_celsius(hour['main']['feels_like'])
        pressure = hour['main']['pressure']
        humidity = hour['main']['humidity']
        weather_main = hour['weather'][0]['main']
        weather_description = hour['weather'][0]['description']
        wind_speed = hour['wind']['speed']
        wind_direction = hour['wind']['deg']
        cloudiness = hour['clouds']['all']
        rain_volume = hour.get('rain', {}).get('3h', 0)
  
  
        weather_data.append({
            "DateTime": date_time,
            "Temperature": temp,
            "Feels like_temp":feels_like,
            "Pressure(hPa)": pressure,
            "Humidity_percent": humidity,

            "Weather": weather_main,
            "Weather Description": weather_description,
            "Wind Speed": wind_speed,
            "Wind Direction": wind_direction,
            "Cloudiness": cloudiness,
            "Rain Volume": rain_volume,
     
        })
        df=pd.DataFrame(weather_data)
else:
    print(f"Failed to get data: {response.status_code}")

```

5. Other transformations made was to format the date to datetime
6. The transformed data was finally loaded into the postgres database with the following comand

   ```

   df.to_sql(name='weather_data',con=engine, index=False)
   ```
