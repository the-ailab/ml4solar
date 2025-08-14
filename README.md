# Solar Power Forecating Across the Continents using ML

## Overview

This project is dedicated to enhancing the performance prediction of solar PV-TE modules across different geographic locations. Utilizing advanced machine learning techniques, we aim to provide accurate predictions of power output and efficiency, contributing to the optimization of solar energy systems worldwide.

## Methodology

We've employed a variety of regressive surrogate machine learning models including Artificial Neural Networks (ANN), Support Vector Regressors (SVR), Gradient Boosting (XGB, LGBM), CatBoost, and Bagging techniques. The models are trained on a rich dataset generated through finite element analysis, incorporating variables such as wind speed, solar irradiance, and ambient temperature. The Jupyter Notebook used for the ML modelling is provided [here](ml4solar.ipynb).


## Results

### Performance Evaluation

Our models have been thoroughly evaluated, and the results highlight their ability to predict power and efficiency with high accuracy. We've summarized the performance of various models like ANN, SVR, XGB, LGBM, CatBoost, and Bagging across different countries in the bar chart below.

![Model Performance Summary](resized_Best_Models_All.png)

*Figure 1: Comparative analysis of machine learning models in predicting solar PV-TE module performance across multiple countries.*

### Model Forecasting Phase

The forecasting phase of our project involved using the optimally trained models to predict the power output and efficiency of solar PV-TE systems for datasets projected into the following year. These predictions are crucial for understanding future performance and planning accordingly.

#### Forecasted Power and Efficiencies

The forecasts display the expected power and efficiency trajectories over time, reflecting the impact of seasonal variations and other factors on the performance of the solar PV-TE systems.

![Forecasting in Antarctica](Antarctica.jpg)
*Figure 2: Forecasted power and efficiencies in Antarctica.*

![Forecasting in Australia](Australia.jpg)
*Figure 3: Forecasted power and efficiencies in Australia.*

![Forecasting in Beijing](Beijing.jpg)
*Figure 4: Forecasted power and efficiencies in Beijing.*

![Forecasting in Berlin](Berlin.jpg)
*Figure 5: Forecasted power and efficiencies in Berlin.*

![Forecasting in Brasilia](Brasilia.jpg)
*Figure 6: Forecasted power and efficiencies in Brasilia.*

![Forecasting in Pretoria](Pretoria.jpg)
*Figure 7: Forecasted power and efficiencies in Pretoria.*

![Forecasting in Washington](Washington.jpg)
*Figure 8: Forecasted power and efficiencies in Washington.*

These figures represent the predictive capabilities of our machine learning models over various locations, indicating robustness and reliability in diverse environmental conditions.

## How to Use

To utilize these forecasting models, data scientists and solar energy analysts can apply the trained models to their local datasets to predict the future performance of solar PV-TE systems. This can aid in strategic planning and optimization for solar energy production.

## Contributions

Contributions to this project are welcome. If you have suggestions or want to improve the forecasting models, please submit a pull request with your proposed changes.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgements

We thank the contributors to the open-source machine learning libraries that have made this analysis possible, as well as the community of researchers dedicated to advancing renewable energy technologies.
