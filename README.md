# Overview

This project demonstrates how everyday tasks can be automated and effectively visualized, **saving both time and resources for scientists and technicians**. 

> **Note**: This project is specifically designed for internal use within Tartu University's local network and is protected by a VPN.  

This repository contains **visualizations of real environmental data** for a specific time period¹², including both low- and high-frequency raw data, as well as calculated EddyPro data, collected from five forest and one peatland eddy covariance towers in Estonia. The data visualized in this repository is the property of the University of Tartu. **Using it without permission is not allowed!**

> **Note**: This repository does not include the Python scripts or codes for half-hourly and daily data downloads from the SQL server, nor the R scripts for data calculations and visualization.

If you'd like to receive scripts/codes directly or request access to the private repository, feel free to contact me via email at svyatoslav.rogozin@gmail.com or svatoslav.rogozin@ut.ee.

**The result of this project is the [Stations and Pulse dashboards](https://svyatoslav-stack.github.io/EcoEddyFluxDashboards/).**

# Stations Dashboard
The Stations Dashboard updates daily in the early morning and holds data for an entire week. The weekly dataset contains approximately 272,160,000 observations (6,048,000 rows and 34 to 56 columns depending on the station), and it is designed for scientists to understand ecosystem fluxes dynamics.

## Workflow Overview

Every night, Python scripts triggered by the Windows Scheduler download high-frequency (HF) and low-frequency (LF) data from the previous day. Due to server delays at various stations, the data update occurs between 02:00 and 05:00 at varying times. Once the daily data is downloaded, the following steps are performed one by one by R scripts automatically:

- Upload data to R environment.
- Correct the time format.
- Calculate the number of missing values in the SQL server data, as well as the count of 'NA' values before and after data quality filtering.
- Apply quality filters to the raw dataset to identify and remove spikes using threshold-based filtering and the Median Absolute Deviation (MAD) method, and provide a summary of the detected spikes.
- Split the daily dataset into half-hourly files for use in EddyPro software.
- Update the massive weekly dataset to ensure continuous tracking of trends.
- Use the RFlux package (implemented in R) to calculate daily fluxes based on metadata and station-specific projects.
- Merge new daily data with the existing EddyPro dataset for ongoing analysis.
- Generate EddyPro calculated data interactive plot.
- Generate LF data interactive plot.
- Create an interactive table displaying the amount of missing data at each step.
- Generate HF data static visualizations (6,048,000 points per plot) using ggplot2 and scattermore packages.
- Create an HTML-based dashboard using the flexdashboard package, making the weekly data easily accessible to scientists.
- Generate backup files, including HTML report, EddyPro files, HF and LF data, and relevant plots to ensure data continuity and easy access.
- Delete all unnecessary files generated during processing to optimize disk space usage and maintain a clean and organized storage environment.

Additional steps:

- Generate a monthly data loss report that quantifies the amount of 30-minute file loss at each station and provides a breakdown of losses by different gas fluxes for the previous month, with the report automatically sent via email.
- Automatically delete datasets older than 10 days to manage storage efficiently.
- Apply time corrections for the switch between winter and summer time twice each year.

Although the system is fully automated, manual intervention is possible if needed to resolve any issues. In case of data absence or problems with the SQL server, the scripts are designed to handle these situations and continue running without crashing.

# Pulse dashboard

The Pulse Dashboard updates every 30 minutes and holds data for an entire day. The daily dataset consists of approximately 9,504,000 observations (864,000 rows and 5 - 12 columns depending on the station), increasing by about 18,000 rows every 30 minutes. The dashboard is designed for technical purposes, primarily to quickly identify issues such as power outages, interference from insects or animals, and sensors/loggers issues.

## Workflow Overview
Every 30 minutes, Python scripts triggered by the Windows Scheduler download both HF and LF data from the previous half-hour. Due to server delays at various stations, data updates typically occur between the 2nd and 7th minute of each half-hour (e.g., at 01:07, 01:37, 02:07, etc.). The dashboard is then updated at the 8th or 9th minute of each half-hour (e.g., at 01:08, 01:38, 02:08, etc.). Handling the data for six stations takes about 1-2 minutes, processing approximately 6 * 18,000 observations in that time. Once the half-hourly data is downloaded, the following steps are automatically performed by R scripts:

- Upload data to R environment.
- Correct the time format and check the latest station time.
- Transform and apply filters to the raw dataset.
- Update the daily dataset to ensure continuous data flow.
- Calculate the number of observations (separately for the gas analyzer and the anemometer) and send an email alert if the count is below a certain threshold (16,000 observations).
- Check the operational status of the gas analyzer and anemometer by calculating the delay between the last observation time and the station's last update.
- Generate interactive visualizations of LF data (such as air temperature and precipitation).
- Display the number of available raw data observations for the last 30 minutes.
- Generate static visualizations of HF data.
- Compile all results in an HTML format using RMarkdown and the Flexdashboard library.

Although the system is fully automated, manual intervention is possible if needed to resolve any issues. In case of data absence or problems with the SQL server, the scripts are designed to handle these situations and continue running without crashing.

# Dashboards Design

The welcome HTML page was designed by me using open-source images, along with HTML and CSS. The dashboards' designs were created using free themes from the open-source Bootstrap framework (Bootswatch).

# Languages, Packages, Tools Used:

## Programming Languages:
- **Python**: Used for downloading data from a remote SQL server (parameters files are not included in the closed repository due to sensitive data such as login credentials). Various Python scripts are also used for supportive tasks.
- **R**: All data handling, calculations, and visualizations are done using R.

### Most used R Libraries and packages:
Data.table, dplyr, fs, Flexdashboard, ggplot2, grid, kableExtra, knitr, lubridate, pandoc, Plotly, plyr, RFlux, readr, reticulate, scales, scattermore.

## Other Tools:
- **Microsoft Power Automate**: Automation of emails, alerts, notifications, and reports.

---

¹ **Stations Dashboard**: Data from 21.11.2024 till 28.11.2024.  
² **Pulse Dashboard**: Data from 28.11.2024.

