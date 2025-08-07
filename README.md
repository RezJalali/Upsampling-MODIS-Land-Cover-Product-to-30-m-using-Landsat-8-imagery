# Upsampling-MODIS-Land-Cover-Product-to-30-m-using-Landsat-8-imagery

This Google Earth Engine project demonstrates an advanced technique for downscaling a low-resolution (500m) MODIS Land Use Land Cover (LULC) product to a high-resolution (30m) map using Landsat 8 imagery and a Random Forest classifier.

The core of this project is the use of seasonal composites to capture vegetation phenology, providing the machine learning model with rich, time-series information to drastically improve classification accuracy over methods that use a single annual composite. 


# Key Features
- Image Downscaling: Upsamples the 500m MODIS LULC product to a detailed 30m resolution, making it suitable for regional-scale analysis.

- Seasonal Feature Engineering: Creates four distinct seasonal composites (Winter, Spring, Summer, Autumn) from a year of Landsat 8 data. This captures the unique phenological signatures of     different land cover types (e.g., the green-up of deciduous forests, the cycles of agriculture).

- Multi-Sensor Fusion: Integrates Landsat 8 optical (Bands 2-7) and thermal (Band 10) data, along with derived spectral indices like NDVI and NDWI, for each season.

- Machine Learning: Employs a smileRandomForest classifier, a robust algorithm well-suited for complex, multi-dimensional remote sensing data.

- Cloud-Native Processing: The entire workflow is implemented on the Google Earth Engine platform, leveraging its computational power and vast data catalog without requiring any local data    downloads.
# Methodology
The workflow is executed in several key stages within the GEE script:

1. Data Ingestion: A full year of Landsat 8 Collection 2, Tier 1 Surface Reflectance imagery is loaded for the specified region. The corresponding MODIS yearly LULC product (MCD12Q1) is also loaded to serve as the source of training labels.

2. Preprocessing & Cloud Masking: A function is applied to the Landsat collection to mask clouds and cloud shadows using the QA_PIXEL band. It also calculates the NDVI and NDWI indices for each image.

3. Seasonal Compositing: The year is divided into four seasons. For each season, the script filters the Landsat imagery, applies the preprocessing function, and creates a cloud-free median composite image. The bands in each seasonal composite are renamed to be unique (e.g., winter_NDVI, summer_SR_B4).

4. Feature Stacking: The four seasonal composites are combined into a single, multi-band image stack. This final stack contains all the spectral and index information for all four seasons and serves as the primary input features for the classifier.

5. Training & Validation: A stratifiedSample method is used to collect training points from the MODIS LULC layer. A robust 70/30 split is performed on the samples to create independent training and validation sets.

6. Model Training and Testing: A Random Forest classifier is trained using the seasonal Landsat stack as input features and the MODIS classes as labels.
The model's performance is evaluated using a confusion matrix on the held-out validation set.

7. Classification: The trained classifier is applied to the entire seasonal Landsat stack to produce the final, high-resolution 30m LULC map.

# How to Use
This script is designed to be run directly in the Google Earth Engine Code Editor.
Copy and paste the entire script into a new file in the GEE Code Editor. add your region of interest as instructed below.

*Define Geometry: You must define your own Area of Interest (AOI) by creating a geometry variable (named as "geometry"). You can either use the drawing tools in the Code Editor to create a polygon or import your own shp file in the assets.*

Run Script: Click the "Run" button. The script will execute all the processing steps and add the final 30m LULC map to the map panel, titled L8_LULC_Upsampled_Seasonal. The validation accuracy will be printed in the "Console" tab.

# Potential Improvements
This project serves as a strong foundation. Future work could include:
* Hyperparameter Tuning: Systematically tune the Random Forest parameters (e.g., numberOfTrees, minLeafPopulation) to further optimize performance.

* Incorporate Additional Data: Add elevation and slope data from a DEM (like SRTM) as auxiliary features, which can significantly help distinguish between certain classes.

* Experiment with Other Classifiers: Test other advanced classifiers available in Earth Engine, such as Gradient Tree Boosting (smileGradientTreeBoost) or Support Vector Machines (libsvm).
