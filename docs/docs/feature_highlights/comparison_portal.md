import ReactPlayer from 'react-player';

# Comparison Portal

Author: `Bohdan Garchu`

**Comparison Portal** allows users to compare two to five countries based on various indicators such as food security, import dependency, balance of trade, food and headline inflation. It further enables the comparison of the regions of a single country against each other.

<ReactPlayer 
  url="/videos/comparison_portal.mp4"
  controls={true}
  width="100%"
  height="auto"
/>

## Features

:::info 
All charts on the comparison portal support download and enlarge functionalities. In addition, line charts support adjustment of x-axis range and bar charts support display of relative values in percent.
:::

* **Country Comparison:** Compare up to five countries against each other
    * **Current Food Security**
        * **People with Insufficient Food Consumption:** Bar chart showing the number of people with insufficient food consumption in each country. 
        * **Population:** The total population of the country
    * **Food Security Trends**
        * **People with Insufficient Food Consumption:** Line chart showing the number of people with insufficient food consumption
        * **People Using Crisis or Above Crisis Food-Based Coping:** Line chart showing the number of people using crisis or above crisis food-based coping strategies
    * **Import Dependency:** Bar chart showing the import dependency of each country in percent of Cereals
    * **Balance of Trade:** Line chart showing the balance of trade of each country in million USD
    * **Food Inflation:** Line chart showing the food inflation of each country in percent
* **Region Comparison:** Compare FCS and rCSI values between some or all of the regions of a single country
    * **Current Food Security**
        * **People with Insufficient Food Consumption:** Bar chart showing the number of people with insufficient food consumption in each region
        * **People Using Crisis or Above Crisis Food-Based Coping:** Bar chart showing the number of people using crisis or above crisis food-based coping strategies in each region
    * **Trend of insufficient food consumption:** Line chart showing the trend of insufficient food consumption in each region