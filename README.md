# Traffic & Weather Patterns

Data 503 Final Project by Avery Pike and Tyler Gomez Riddick

## Overview

**Traffic & Weather Patterns** analyzes the impact of weather conditions on urban traffic incidents in Portland, OR and Seattle, WA. The project collects real-time weather and traffic incident data, stores it in a PostgreSQL database, and visualizes trends using R. Docker is used to automate and orchestrate data collection via scheduled API calls.

## Project Workflow

1. **Data Collection (Docker & API)**
   - API endpoints (see below) provide city-specific weather and traffic data, including temperature, pressure, visibility, weather description, incident type, and incident counts.
   - Docker containers run scheduled cron jobs to pull data from these APIs at regular intervals, ensuring up-to-date and consistent data collection.

2. **Data Storage (PostgreSQL)**
   - Collected data is ingested into a PostgreSQL database for persistent, structured storage and easy querying.

3. **Data Analysis & Visualization (R)**
   - The RMarkdown file (`DE_analysis.Rmd`) loads and analyzes the merged datasets (`portland_joined.csv`, `seattle_joined.csv`), which combine weather and incident data for each city.
   - Analyses include grouping incident types by weather condition, normalizing for weather frequency, and generating visualizations (e.g., bar charts of weighted and absolute incident counts).
   - Plots are exported as PNGs for reporting.

## API Documentation

- [Main API Docs](https://pikegomezapi-production.up.railway.app/docs): Interactive documentation.
- [CityWeather API](https://pikegomezapi-production.up.railway.app/cityweather): Returns city, timestamp, temperature (F), pressure (hPa), visibility (meters), weather description, incident type, and incident count.
- [Portland Data](https://pikegomezapi-production.up.railway.app/portland): Same as above, limited to Portland, OR.
- [Seattle Data](https://pikegomezapi-production.up.railway.app/seattle): Same as above, limited to Seattle, WA.

## Technologies Used

- **Docker**: Orchestrates and schedules API data collection via cron jobs.
- **PostgreSQL**: Stores weather and incident data for reliable access and querying.
- **R**: Performs data cleaning, analysis, and creates visualizations (see `DE_analysis.Rmd`).

## Example R Workflow

```r
# Load libraries
library(tidyverse)

# Read merged datasets
pdx <- read_csv('portland_joined.csv')
sea <- read_csv('seattle_joined.csv')

# Clean and analyze
pdx <- unique(pdx)
sea <- unique(sea)

# Visualization example
pdx %>%
  mutate(type = factor(case_when(
    type == 1 ~ 'Construction',
    type == 2 ~ 'Event',
    type == 3 ~ 'Congestion',
    type == 4 ~ 'Accident'
  )),
  weather = factor(weather, levels = c('Rain', 'Clouds', 'Clear'))) %>%
  filter(type %in% c('Congestion', 'Accident')) %>%
  group_by(weather, type) %>%
  summarize(count = n()) %>%
  ggplot(aes(x=weather, y=count, fill=type)) +
  geom_bar(position='dodge', stat='identity', color='black') +
  labs(title='Portland Traffic Incidents by Weather', x='Weather', y='Incidents') +
  theme_gray()
```

## File Structure

- `DE_analysis.Rmd`: Main data analysis and visualization notebook (RMarkdown).
- `portland_joined.csv`, `seattle_joined.csv`: Combined datasets for each city.
- Docker and API scripts (see repo for details).

## Getting Started

1. Clone the repository:
   ```
   git clone https://github.com/tpriddick/traffic_weather_patterns.git
   ```
2. Spin up Docker containers to run and schedule API calls (see Docker-related files/scripts).
3. Ensure you have PostgreSQL set up and accessible for the data pipeline.
4. Use R or RStudio to open `DE_analysis.Rmd` and generate visualizations.

## Team

- Tyler Gomez Riddick ([LinkedIn](https://www.linkedin.com/in/tyler-gomez-riddick/) | [Portfolio](https://www.datascienceportfol.io/tylergomezriddick))
- Avery Pike ([LinkedIn](https://www.linkedin.com/in/averypike/))

## Contributing

Contributions and suggestions are welcome. Please open an issue or submit a pull request.

## License

No license specified.

---

Project by [@tpriddick](https://github.com/tpriddick)
