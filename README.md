# San Francisco Crime: Geospatial Analysis

Notebook: [sf_crime_geospatial.ipynb](sf_crime_geospatial.ipynb) (text in Portuguese)
Data: [SF Crime Classification](https://www.kaggle.com/competitions/sf-crime) on Kaggle, 878,049 labelled records (train) plus 884,262 unlabelled (test), January 2003 to May 2015.

The brief was to identify the three most critical areas of San Francisco and recommend where to put new police stations and what each should specialise in.

## What I did

**Two working tables.** Train and test share date, district, address and coordinates, so I merged them into a 1.76M-row table for everything geographic and temporal, and used the 878k labelled rows for anything involving category, description or resolution.

**Coordinates.** 143 records carry a sentinel location (X=−120.5, Y=90) rather than NaN. Instead of dropping them I imputed in cascade: median of the same normalised address (22 records, metre-level precision), then median of the district (121 records), with a `coord_imputed` flag so they can be excluded from fine-grained maps.

**Temporal and categorical EDA.** Crime is a daytime phenomenon: the afternoon has 291k records, 2.3× the early morning (125k), with peaks at 12:00 and 18:00. The minimum at 05:00 is still about 9k per hour. Weekdays vary by only 15%. Property crime is 54% of the labelled records, and the average resolution rate is about 39%, highest for drug and warrant offences and lowest for theft. A hour × weekday heatmap separates property crime (midday) from violent crime (weekend early hours).

**Spatial structure.** K-Means with 8 clusters on coordinates; the three largest hold 66% of all records and sit on a single north–south axis only 0.04° of longitude wide. DBSCAN on a sample found 119 dense micro-hotspots, notably Bryant St and Mission/16th St. A network graph of category × district co-occurrence confirmed the Larceny/Theft–Southern link.

**Coverage of existing stations.** A Voronoi diagram over the current stations, crossed with crime density, shows two opposite problems: Tenderloin has the smallest radius (0.52 km) but 207k crimes, while Taraval averages 1.90 km and 66% of its crimes happen more than 1.5 km from the station. For the new stations I used a 1 km radius, which captures about 60% of each cluster's crimes.

**Socioeconomic layer.** Joining ACS 2005–2009 and Census 2010 indicators by district: unemployment and drug crime correlate at r = 0.82 (Tenderloin: 18.5% unemployment, 22.5% drug offences). Southern is one of the richer districts yet has the most violence per resident, because it covers the commercial core that draws people from the whole city.

## Results

| Station | District | Volume | Specialty | Resolution rate | Peak hour |
|---|---|---|---|---|---|
| Alpha | Southern | ~290k | Property crime and drugs | ~39% | 18:00 |
| Beta | Mission | ~161k | Property crime, general | ~37% | 17:00 |
| Gamma | Northern | ~126k | Property and street crime | ~41% | 18:00 |

Alpha absorbs the overlap of Southern, Tenderloin and Central and needs two distinct capabilities: theft for the Southern side and drugs and public order for the Tenderloin side. Beta sits only 0.30 km from the existing Mission station but still improves coverage for about 60k crimes on the edge of its zone.

## What I would improve

K-Means on raw coordinates ignores density, which is why I added DBSCAN as a check; a density-weighted method from the start would be cleaner. The census join is at district level, which hides variation inside large districts. The recommendation weighs all crimes equally; weighting by severity would likely move Gamma.

---

This case is one of seven in my [data science portfolio](https://github.com/juliapmonteirojm-lab/data-science-portfolio).
