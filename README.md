# Research on Apartment Sales Advertisements

Exploratory data analysis of Yandex Realty's archive of apartment sale listings from Saint Petersburg and neighboring settlements, collected over several years. The goal is to understand what drives the market value of a property well enough to eventually support an automated system for flagging pricing anomalies and potential fraud.

## About the data

Each listing combines two kinds of fields:

- **User-entered data** — price, area, number of rooms, ceiling height, floor, etc.
- **Automatically generated data** — distance to the city center and airport, number of nearby parks and bodies of water, pulled from cartographic/geo-services.

The raw file is a tab-separated CSV (`real_estate_data (8).csv`) with 22 columns, including:

| Column | Description |
|---|---|
| `last_price` | Listing price |
| `total_area`, `living_area`, `kitchen_area` | Area measurements (m²) |
| `rooms` | Number of rooms |
| `ceiling_height` | Ceiling height (m) |
| `floor`, `floors_total` | Floor and building height |
| `is_apartment` | Whether the property is a studio apartment |
| `locality_name` | Settlement/city |
| `airports_nearest`, `cityCenters_nearest` | Distance to airport/city center (m) |
| `parks_around3000`, `parks_nearest`, `ponds_around3000`, `ponds_nearest` | Nearby green space and water features |
| `days_exposition` | Days the listing stayed active |

## What the notebook does

1. **Data preprocessing**
   - Fills missing values using column-appropriate logic (median for `ceiling_height`, area ratios for `living_area`/`kitchen_area`, zero for `balcony`/`parks_around3000`/`ponds_around3000`, `False` for `is_apartment`).
   - Leaves structurally meaningful NaNs alone (e.g. `days_exposition`, distance fields) rather than imputing them.
   - Drops rows with negligible missingness (`floors_total`, `locality_name`).
   - Fixes data types (dates to datetime, categorical fields to int/float).
   - Standardizes duplicated/inconsistent locality names.
   - Engineers new features: price per square meter, floor type (first/last/other), distance to center, and day/month/year of publication.

2. **Outlier detection & filtering**
   - Uses histograms and boxplots to inspect the distribution of each key parameter (living area, total area, kitchen area, room count, ceiling height, distance to center, price, listing duration, etc.).
   - Applies reasoned cutoffs to build a cleaned `filtered_data` table (e.g. total area 20–250 m², kitchen area 3–40 m², price up to 50,000,000 rubles).

3. **Exploratory analysis**
   - Examines how price relates to total/living/kitchen area, room count, floor, publication day/month/year, and distance to the center.
   - Compares average price per m² across the 10 most common localities.
   - Summarizes typical listing duration (median, mean, quartiles) and what counts as a "fast" vs. "prolonged" sale.

## Key findings

- **Area is the dominant price driver.** Total and living area correlate most strongly with price; kitchen area matters less.
- **Location matters.** Price per m² is highest in Saint Petersburg itself and falls off sharply within the first 5–10 km from the center before stabilizing.
- **Floor effects.** Ground-floor units are cheaper; top-floor units command a premium, likely for views and quiet.
- **Room count.** Average price rises with room count, with a notable jump from 3 rooms onward.
- **Timing barely matters.** Day, month, and year of listing publication have little to no effect on price (aside from a broader 2014 market-level shift).
- **Sale speed.** Median time on market is roughly 94–98 days; about a quarter of listings sell within ~42 days (fast sales), while those active beyond ~227 days are considered prolonged.
- **Typical apartment profile:** ~50–60 m² total area, ~30 m² living area, ~10 m² kitchen, 1–2 rooms, mid-level floor in a 5–10 story building, ceiling height 2.5–2.7 m, 10–15 km from the center, priced around 4–6.5 million rubles, selling in 1.5–3 months.

## Requirements

- Python 3
- pandas
- matplotlib
- seaborn

## Usage

1. Place `real_estate_data (8).csv` in the same directory as the notebook.
2. Open `real_estate.ipynb` and run all cells top to bottom.

## Project structure

```
.
├── real_estate.ipynb          # Analysis notebook
├── real_estate_data (8).csv   # Source data (not included)
└── README.md
```
