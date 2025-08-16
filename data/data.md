# Data Documentation

## Overview

This document provides comprehensive documentation for the ML4SOLAR dataset, which combines NASA-sourced environmental data with solar photovoltaic-thermoelectric (PV-TE) module performance data generated through finite element analysis. The dataset enables machine learning-based forecasting of solar power output and efficiency across seven diverse geographic locations.

## Data Sources and Methodology

### Primary Data Sources
- **NASA Renewable Energy Data**: Meteorological and solar irradiation measurements
- **Finite Element Analysis**: Solar PV-TE module performance simulations

### Geographic Coverage
The dataset encompasses seven strategically selected locations representing diverse climatic conditions:
- Antarctica
- Australia  
- Beijing, China
- Berlin, Germany
- Brasilia, Brazil
- Pretoria, South Africa
- Washington, USA

## Directory Structure

```
data/
├── learning/                           # Training datasets
│   ├── raw_files/                     # Original NASA data with metadata
│   │   ├── irradiation/              # Solar irradiation measurements
│   │   └── meteorology/              # Weather measurements
│   ├── cleaned_and_joined_dataset_files/  # Full feature datasets
│   ├── cleaned_and_joined_subset_files/   # Reduced feature datasets
│   └── train_data_power_eff_viz/          # Final training data with targets
└── forecasting/                        # Test/validation datasets
    ├── raw_forecasting_data/           # Original forecasting data
    ├── cleaned_forecasting_data_subset/ # Processed forecasting features
    └── forecasting_data_power_eff_viz/  # Forecasting data with targets
```

## Dataset Descriptions

### Raw Data (`learning/raw_files/`)

#### Irradiation Data (`irradiation/`)
Contains NASA-sourced solar irradiation measurements for each location with the following variables:

| Column | Description | Units |
|--------|-------------|-------|
| Observation period | Timestamp in ISO 8601 format | - |
| TOA | Irradiation on horizontal plane at top of atmosphere | Wh/m² |
| Clear sky GHI | Clear sky global horizontal irradiation at ground level | Wh/m² |
| Clear sky BHI | Clear sky beam horizontal irradiation at ground level | Wh/m² |
| Clear sky DHI | Clear sky diffuse horizontal irradiation at ground level | Wh/m² |
| Clear sky BNI | Clear sky beam normal irradiation (sun-tracking) | Wh/m² |

**Note**: Each CSV file contains approximately 20 lines of metadata at the beginning, providing measurement specifications and quality indicators.

#### Meteorology Data (`meteorology/`)
Contains NASA-sourced weather measurements with the following variables:

| Column | Description | Units |
|--------|-------------|-------|
| Temperature | Air temperature at 2m above ground | K |
| Relative humidity | Relative humidity at 2m above ground | % |
| Pressure | Atmospheric pressure at ground level | hPa |
| Wind speed | Wind speed at 10m above ground | m/s |
| Wind direction | Wind direction at 10m above ground (0°=North, 90°=East) | degrees |
| Rainfall | Precipitation as rain depth | kg/m² (mm) |
| Snowfall | Precipitation as snow | kg/m² |
| Snow depth | Accumulated snow depth | m |
| Short-wave irradiation | Surface incoming shortwave irradiation | Wh/m² |

### Processed Training Data (`learning/`)

#### Full Feature Dataset (`cleaned_and_joined_dataset_files/`)
This folder contained meteorological and irradiation data merged after metadata removal, containing:
- **Date**: Temporal reference
- **Temperature**: Air temperature (K)
- **Relative Humidity**: Humidity percentage (%)
- **Pressure**: Atmospheric pressure (hPa)
- **Wind speed**: Wind velocity (m/s)
- **Wind direction**: Wind bearing (degrees)
- **Rainfall**: Precipitation (kg/m²)
- **Snowfall**: Snow precipitation (kg/m²)
- **Snow depth**: Snow accumulation (m)
- **Short-wave irradiation**: Incoming solar radiation (Wh/m²)
- **TOA**: Top-of-atmosphere irradiation (Wh/m²)
- **Clear sky GHI**: Global horizontal irradiation (Wh/m²)
- **Clear sky BHI**: Beam horizontal irradiation (Wh/m²)
- **Clear sky DHI**: Diffuse horizontal irradiation (Wh/m²)
- **Clear sky BNI**: Beam normal irradiation (Wh/m²)

#### Subset Feature Dataset (`cleaned_and_joined_subset_files/`)
This folder contained reduced feature set optimized through feature selection analysis:
- **Date**: Temporal reference
- **Temperature**: Air temperature (K)
- **Wind speed**: Wind velocity (m/s)
- **Clear sky GHI**: Global horizontal irradiation (Wh/m²)

#### Training Data with Targets (`train_data_power_eff_viz/`)
This folder contained final training datasets containing input features and finite element-derived targets:
- **Input Features**: Date, Temperature, Wind speed, Clear sky GHI
- **Target Variables**: 
  - **Power Output**: Solar PV-TE module power generation (W)
  - **Efficiency**: Module conversion efficiency (%)

*Target variables were generated using finite element analysis simulations that model the complex thermodynamic and photovoltaic processes within solar PV-TE modules under varying environmental conditions.*

### Forecasting Data (`forecasting/`)

The forecasting directory mirrors the learning directory structure but contains temporal holdout data for model validation and future predictions. Due to feature optimization insights from training, only subset configurations are maintained for forecasting datasets.

## Data Processing Pipeline

### 1. Metadata Removal
Raw NASA files contain approximately 20 lines of header metadata that were systematically removed using Python preprocessing:

```python
# Skip metadata rows and load data
def cleaner(file_path, met_or_irr = 'met'):
    
    # opening and reading files
    open_file = open(file_path, 'r')
    read_file = open_file.read()

    # splitting by '\n#'
    new_read = read_file.split('\n#')
    
    # keeping just the last value of that list
    clean = new_read[-1]
```

### 2. Data Integration
Meteorological and irradiation datasets were temporally aligned and merged:

```python
for name in country_names:
    # first step is to clean both irradiation and meteorology files
    irrad = cleaner('file.csv', met_or_irr= 'irr')
    meteor = cleaner('file.csv',)
    
    # reading both saved files
    irrad_set = pd.read_csv('clean_irr.csv', sep = ';')[cols_irradiation]
    meteor_set = pd.read_csv('clean_met.csv', sep = ';')[cols_meteorology]
    
    print('no of rows in both sets is {} and {}'.format(len(irrad_set), len(meteor_set)))
    
    # Concatenating both tables
    print('Joining tables to get extracted cols for {}======='.format(name))
    joined = pd.concat([meteor_set, irrad_set], axis = 1 )
```

### 3. Feature Engineering
Key preprocessing transformations applied:

#### GHI Normalization
Clear sky GHI values were scaled by a factor of 8 to convert from daily accumulated irradiation to representative hourly values:

```python
joined['Clear sky GHI'] = joined['Clear sky GHI'] / 8
```

**Rationale**: NASA data provides daily accumulated irradiation values. Dividing by 8 approximates the effective solar hours per day, providing a more representative instantaneous irradiation measure suitable for PV-TE module performance modeling.

#### Temperature Unit Standardization
Temperature values maintained in Kelvin for consistency with thermodynamic calculations in finite element simulations.

### 4. Quality Assurance
- Missing value identification and interpolation
- Outlier detection using statistical thresholds
- Temporal consistency validation

## Finite Element Analysis Integration

Solar PV-TE module performance targets were generated through comprehensive finite element modeling that incorporates:

- **Thermal Dynamics**: Heat transfer mechanisms within module components
- **Photovoltaic Conversion**: Solar-to-electrical energy conversion efficiency
- **Thermoelectric Effects**: Temperature differential-based power generation
- **Environmental Coupling**: Integration of meteorological conditions into physical models

This approach ensures that target variables reflect realistic module behavior under diverse environmental conditions, providing robust ground truth for machine learning model training.

## Data Quality Metrics

### Temporal Coverage for each Location
- **Training Period**: 2019-01-01 to 2021-12-31
- **Forecasting Period**: 2022-01-01 to 2022-12-31
- **Data Completeness**: >95% across all locations

### Statistical Summary (Key Features by Location)

| Location | Temperature (°c) | Wind Speed (m/s) | Clear Sky GHI (Wh/m²) | Power Output (W) | Efficiency (%) |
|----------|-----------------|------------------|----------------------|------------------|----------------|
| | Min/Max/Mean±SD | Min/Max/Mean±SD | Min/Max/Mean±SD | Min/Max/Mean±SD | Min/Max/Mean±SD |
| **Antarctica** | -66.0/-14.1/-40.9±12.3 | 0.4/13.4/7.2±2.5 | 0.0/1467.2/449.9±527.5 | 0.0/38.1/11.4±13.7 | 0.0/13.5/8.0±6.0 |
| **Australia** | 15.1/37.2/28.1±4.9 | 0.2/12.0/3.8±1.6 | 532.2/1208.4/885.5±206.8 | 14.6/33.5/24.4±5.8 | 13.0/13.5/13.4±0.2 |
| **Beijing** | -17.6/33.1/12.5±11.4 | 0.1/8.8/2.4±1.5 | 159.6/1109.0/626.3±245.2 | 3.4/31.1/17.4±7.4 | 11.4/13.5/13.0±0.5 |
| **Berlin** | -10.7/29.3/10.5±8.1 | 0.2/11.4/4.1±1.8 | 112.4/1075.9/566.9±321.9 | 2.0/30.2/15.1±9.5 | 10.9/13.5/12.7±0.9 |
| **Brasilia** | 23.1/33.6/27.0±2.3 | 0.0/1.4/0.7±0.3 | 681.4/1020.2/874.1±89.8 | 20.1/29.7/25.6±2.5 | 13.4/13.5/13.5±0.0 |
| **Pretoria** | 4.2/28.4/18.6±4.7 | 0.1/7.7/2.5±1.3 | 485.6/1182.6/843.4±215.6 | 13.1/32.6/23.7±6.0 | 12.9/13.5/13.4±0.2 |
| **Washington** | -11.2/31.0/14.0±9.7 | 0.1/10.0/2.9±1.5 | 308.0/1120.6/711.6±244.5 | 7.1/31.0/19.7±7.3 | 12.1/13.5/13.2±0.4 |

## Usage Guidelines

### For Researchers
1. **Model Training**: Use `train_data_power_eff_viz/` for supervised learning
2. **Feature Selection**: Reference subset files for optimal feature combinations
3. **Validation**: Employ forecasting data for temporal holdout validation

## Data Limitations

- **Geographic Scope**: Limited to seven representative locations
- **Temporal Constraints**: Finite observation period may not capture extreme weather events
- **Model Assumptions**: Finite element simulations based on idealized module configurations
- **Measurement Uncertainty**: Inherent NASA sensor accuracy limitations

## Citation and Attribution

When using this dataset, please cite:
- NASA Renewable Energy Data Portal for raw environmental measurements
- This project for finite element-derived performance targets and processing methodology