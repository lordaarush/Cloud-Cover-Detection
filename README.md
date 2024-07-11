# CCD-Cloud-Coverage-Detection
## Problem Statement
To detect the cloud cover percentage for future given 6 hours of past data


## Introduction
In the realm of meteorology and environmental monitoring, accurate detection and prediction of cloud coverage play a crucial role in various applications, from weather forecasting to climate research. A predictive model for cloud coverage detection leverages advanced machine learning techniques to analyze satellite imagery and other meteorological data to predict the extent and distribution of clouds. By utilizing features such as temperature, humidity, and historical cloud patterns, this model can provide timely and precise predictions, aiding in the optimization of solar energy systems, agricultural planning, and aviation safety. The development of such a model not only enhances our understanding of atmospheric phenomena but also contributes to more reliable and efficient decision-making processes in weather-dependent sectors.

## Dataset
Dataset consisted of various features including the cloud cover percentage along with the date and time. Dataset had following features for each row:


##### 1. Direct sNIP [W/m^2]
Direct Normal Irradiance (DNI) measured using a sensor such as sNIP (Spectral Normal Incidence Pyrheliometer), representing the amount of solar radiation received per unit area directly from the sun, expressed in watts per square meter.
##### 2. Azimuth Angle [degrees]
The compass direction from which the sunlight is coming, measured in degrees from North.
##### 3. Tower Dry Bulb Temp [deg C]
The air temperature measured by a thermometer freely exposed to the air but shielded from radiation and moisture, expressed in degrees Celsius.
##### 4. Tower Wet Bulb Temp [deg C]
The temperature a parcel of air would have if it were cooled to saturation (100% relative humidity) by the evaporation of water into it, expressed in degrees Celsius.
##### 5. Tower Dew Point Temp [deg C]
The temperature at which air becomes saturated with moisture and dew forms, expressed in degrees Celsius.
##### 6. Tower RH [%]
Relative Humidity, the amount of moisture in the air compared to what the air can hold at that temperature, expressed as a percentage.
##### 7. Peak Wind Speed @ 6ft [m/s]
The highest wind speed recorded at a height of 6 feet above the ground, expressed in meters per second.
##### 8. Avg Wind Direction @ 6ft [deg from N]
The average direction from which the wind is blowing at a height of 6 feet, measured in degrees from North.
##### 9. Station Pressure [mBar]
Atmospheric pressure measured at the station's location, expressed in millibars.
##### 10. Precipitation (Accumulated) [mm]
The total amount of precipitation (rain, snow, sleet, etc.) accumulated over a specific period, expressed in millimeters.
##### 11. Snow Depth [cm]
he depth of snow on the ground at a specific location, expressed in centimeters.
##### 12. Moisture
Likely refers to soil moisture content or atmospheric moisture level, which could be expressed in various units depending on the context.
##### 13. Albedo (CMP11)
The fraction of solar energy (shortwave radiation) reflected from the Earth back into space, measured using a CMP11 pyranometer.
##### 14. Total Cloud Cover [%]
The percentage of the sky covered by clouds, expressed as a percentage.
##### 15.MST (Mountain Standard Time):
The local time in the Mountain Time Zone, which is used to timestamp the data.
##### 15. Global CMP22 (vent/cor) [W/m^2]:
Measurement of the global solar radiation received on a horizontal surface, corrected and ventilated using the CMP22 pyranometer, expressed in watts per square meter.

## Data Preprocessing
Data points for duration of 1 year was given. Few data points here are there were missing which were imputed using linear interpolation. 

Dataset was then, resampled at a gap of 2 minutes per sample. Hence, 180 consecutive datapoints will span over 6 hours. A sliding window sampling of size 180 was run on the dataset on each day starting from 12pm midnight till the end of day. This had to be done because cloud coverage data and some other features for night time was not present in the dataset. And depending on month, timining between which data was present changed. In winter months such as january, december, november, data was available after 8 AM but, for summer months, data was available as early as 5 AM. Hence, it was decided to not to span sliding window between two days. 

## Architecture

Architecture chosen for this model is reminiscent of transformer architecture. However, instead of using positional embeddings, we are using LSTMs to feed the context of time. The architecture consits of two parts
#### 1. Encoder
Encoder is an lstm layer followed by an multihead attention with 8 heads followed by feed forward neural network and skip connections between output of attention layer and output of feed forward neural network and normalizing the layer.
#### 2. Decoder
Decoder is basically like encoder with LSTM follwed by multihead attention layer with 8 heads followed by feed forward neural network and skip connections between output of attention layer and output of feed forward layer and a normalizing layer at the end. Except, cross attention is present in decoder layer. Key and value from the encoder is fed to the key and value of the decoder multihead attention layer. Also, hidden states of decoder LSTM are initialized with final hidden state of encoder LSTM layer. If there are n encoder and n decoder blocks, then, hidden state of first encoder block will be used to initialize nth decoder LSTM, similarity 2nd encoder LSTM hidden state will be transferred to n-1th decoder LSTM and so on. 

![](https://github.com/Ishan130803/CCD-Cloud-Coverage-Detection/blob/main/Attention%20Model%20Architecture.png)
<div align="center">
  <p><em>Model Architecture</em></p>
</div>

## Results
Model was trained for 20 epochs however model at 12th epoch had highest validation score. 
R2 Score was chosen as satisficing metric for evaluation. Also, instead of evaluating at all time stamps in future, timestamps at 15 minute in future, 25 minute in future and 30 minute in future was selected.

<div align="center">

| Time     | R² Score   |
|----------|------------|
| 15 Min   | 0.883098   |
| 25 Min   | 0.8283565  |
| 30 Min   | 0.7982018  |

</div>

To visualize this, sequences at a gap of 15 samples were taken (means 30 minutes gap between end of one sequence and starting of next sequence) and predictions were calculated and plotted as follows:

![image](https://github.com/Ishan130803/CCD-Cloud-Coverage-Detection/assets/96647844/f2a09506-eea4-4e65-8efc-b8fa55e770eb)


## Conclusion
In conclusion, the project focused on developing a robust system for detecting cloud coverage using lagged features, harnessing advanced machine learning techniques and novel architecture. By leveraging historical data and extracting meaningful temporal patterns, the model was able to effectively predict cloud coverage at future time points. 

