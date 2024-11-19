# Climate-Trend-Analysis
This repository houses scripts that can be used to run a climate trend analysis in Google Earth Engine


**Prerequisites:**
- [ ] [Select an appropriate data source according to literature for eg. CHIRPS, ERA5, MODIS etc.](#generating-a-project-)
- [ ] [Basic Javascript knowledge](#adding-data-)
- [ ] [Basic GEE knowledge](#adding-data-)

# Generating trends in GEE

To generate temperature trends use the following [[script]](https://code.earthengine.google.com/0349a7632327e3544eff4a1f93adfa67)
```bash
//import country boundary
var dataset = ee.FeatureCollection("USDOS/LSIB_SIMPLE/2017")


//Filter the country boundary to study area
var kenya = dataset.filter(ee.Filter.eq('country_na', 'Kenya')) //can change to region of interest
Map.centerObject(kenya,6)

//Map.addLayer(dataset,{}, 'World Country')
Map.addLayer(kenya,{}, 'Kenya')

// #############################################################################

//Load data collection
var dataset = ee.ImageCollection("ECMWF/ERA5_LAND/MONTHLY_AGGR")
.select('temperature_2m')

// Scale to Kelvin and convert to Celsius, set image acquisition time.
var dataset_convert = dataset.map(function(img) {
  return img
    .subtract(273.15)
    .copyProperties(img, ['system:time_start']);
});

//define date range of interest 
var Era5_TREND = dataset_convert.filterDate('1981-01-01','2023-12-31');

var MeanAirTemp = Era5_TREND.mean()

//Chart the full time series for your AOI
var TS1 = ui.Chart.image.series (Era5_TREND, kenya, ee.Reducer.mean(),1000,'system:time_start').setOptions({
  title:'Average LST from 1981-2023(ERA5)', 
  vAxis: {title: '°C'}, });

print(TS1);

var tempVisParams = {
  min:15,
  max:30,
  palette: ['2b83ba','abdda4','ffffbf', 'ff6227', 'ff4500']
}
Map.addLayer(MeanAirTemp.clip(kenya), tempVisParams, 'Mean AirTemp 1981-2023')
```
## Potential Output
![openmapflow-pipeline](assets/pipeline.png)

To generate precipitation trends use the following [[script]](https://code.earthengine.google.com/8c7f92b47009c0bf9707ed040aebd368)


To  quantify  trends  in  each  pixel  using  the  non-parametric  Mann-Kendall  test  for trend  significance  and  Sen’s  slope  estimator  for  magnitude resort to this
script


Once all data is extracted and processed, inside GEE it can be exported:
- [ ] For further visualization in ArcGIS Pro
- [ ] To R, where the Mann-Kendall and Sen’s slope tests can be applied using the ’trend’ package.



# Citation
```
@article{nakalembe202440,
  title={A 40-Year Remote Sensing Analysis of Spatiotemporal Temperature and Rainfall Patterns in Senegal},
  author={Nakalembe, Catherine and Frimpong, Diana Botchway and Kerner, Hannah Rae and Sarr, Mamadou Adama},
  year={2024},
  publisher={EarthArXiv}
}
