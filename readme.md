# Airbnb Data Analysis 🏡📊
  
---                          

## 🚀 Project Overview

This repository contains an Exploratory Data Analysis (EDA) of Airbnb listings data. The goal is to extract actionable insights about listing prices, room types, neighborhood distributions, price relationships, and review trends over time. This analysis can guide hosts, guests, and stakeholders in understanding market patterns and making data-driven decisions.

Key questions addressed:

1. What is the distribution of listing prices?
2. How are different room types distributed?
3. How are listings distributed across neighbourhood groups?
4. What is the relationship between price and room type?
5. How has the number of reviews changed over time?

---

## 📂 Repository Structure

```
├── data/
│   └── listings.csv               # Raw dataset (Airbnb listings CSV)
├── notebooks/
│   └── Airbnb_Data_Analysis.ipynb # Jupyter Notebook with EDA steps and code
├── images/                        # Saved visualizations
│   ├── distribution_price.png
│   ├── room_type_distribution.png
│   ├── neighborhood_distribution.png
│   ├── price_vs_room_type.png
│   └── reviews_over_time.png
├── requirements.txt               # Python dependencies
└── README.md                      # This README file
```

> **Note**: Ensure that your dataset file `listings.csv` is placed in the `data/` directory. Save figures from the notebook into the `images/` folder using descriptive filenames as shown above.

---

## 🛠️ Setup & Installation

1. **Clone the repository**:

   ```bash
   git clone https://github.com/<your-username>/airbnb-data-analysis.git
   cd airbnb-data-analysis
   ```

2. **Create a virtual environment** (recommended):

   ```bash
   python3 -m venv venv
   source venv/bin/activate      # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**:

   ```bash
   pip install -r requirements.txt
   ```

4. **Prepare the data**:

   - Place your Airbnb listings CSV (e.g., `listings.csv`) in the `data/` folder.

5. **Launch the Jupyter Notebook**:

   ```bash
   jupyter lab notebooks/Airbnb_Data_Analysis.ipynb
   ```

   or

   ```bash
   jupyter notebook notebooks/Airbnb_Data_Analysis.ipynb
   ```

---

## ⚙️ Requirements

Create a `requirements.txt` with at least:

```
pandas
numpy
matplotlib
seaborn
jupyterlab   # or notebook
```

Install with:

```bash
pip install -r requirements.txt
```

---

## 📊 Exploratory Data Analysis (EDA)

Below is a summary of each analysis step, including code snippets, resulting visuals, and insights. For full code and commentary, refer to `notebooks/Airbnb_Data_Analysis.ipynb`.

### 1. Distribution of Listing Prices 🏷️

- **Objective**: Understand the spread, skewness, and outliers in listing prices.

- **Data Cleaning**:

  - Remove `$` and `,` from price strings: `df['price'] = df['price'].replace(r'[\\$,]', '', regex=True).astype(float)`

- **Visualization**:

  ```python
  plt.figure(figsize=(10,6))
  sns.histplot(df['price'], bins=50, kde=True, color='steelblue')
  plt.title('Distribution of Listing Price')
  plt.xlabel('Price ($)')
  plt.ylabel('Frequency')
  plt.savefig('images/distribution_price.png', bbox_inches='tight')
  plt.show()
  ```

- **Insights**:

  - The distribution is right-skewed: many listings at lower price ranges, fewer at high price.
  - Significant outliers at very high prices; consider filtering or capping for downstream analysis.

---

### 2. Room Type Distribution 🛏️

- **Objective**: Determine the frequency of each room type category.

- **Visualization**:

  ```python
  plt.figure(figsize=(8,5))
  sns.countplot(x='room type', data=df, color='hotpink')
  plt.title('Room Type Distribution')
  plt.xlabel('Room Type')
  plt.ylabel('Count')
  plt.xticks(rotation=45)
  plt.savefig('images/room_type_distribution.png', bbox_inches='tight')
  plt.show()
  ```

- **Insights**:

  - Identify the most common category (e.g., Entire home/apt, Private room).
  - Low-frequency types (Shared room, Hotel room) may need grouping or special handling.

---

### 3. Listings by Neighbourhood Group 🗺️

- **Objective**: Compare the number of listings across neighbourhood groups.

- **Preprocessing**:

  - Clean inconsistent labels (e.g., lowercase duplicates).

- **Visualization**:

  ```python
  order = df['neighbourhood_group'].str.title().value_counts().index
  plt.figure(figsize=(12,8))
  sns.countplot(y='neighbourhood_group', data=df, order=order, color='lightgreen')
  plt.title('Number of Listings by Neighbourhood Group')
  plt.xlabel('Count')
  plt.ylabel('Neighbourhood Group')
  plt.savefig('images/neighborhood_distribution.png', bbox_inches='tight')
  plt.show()
  ```

- **Insights**:

  - Major areas (e.g., Manhattan, Brooklyn) often dominate supply.
  - Check for anomalies or mislabeled groups; ensure consistent capitalization.
  - Normalize counts by population or area when making comparisons.

---

### 4. Price vs Room Type 💲 vs 🛏️

- **Objective**: Explore how price varies across room types.

- **Visualization**:

  ```python
  plt.figure(figsize=(10,6))
  sns.boxplot(x='room type', y='price', data=df, palette='Set2')
  plt.title('Price vs Room Type')
  plt.xlabel('Room Type')
  plt.ylabel('Price ($)')
  plt.xticks(rotation=45)
  plt.savefig('images/price_vs_room_type.png', bbox_inches='tight')
  plt.show()
  ```

- **Insights**:

  - Entire homes/apartments typically have higher median prices than private rooms.
  - Spread and outliers show variability; some room types exhibit extreme high-priced listings.
  - Useful for pricing strategy or feature engineering in prediction models.

---

### 5. Reviews Over Time 📈📝

- **Objective**: Track number of reviews over time to identify trends, seasonality, or anomalies.

- **Data Preparation**:

  ```python
  df['last_review'] = pd.to_datetime(df['last_review'], errors='coerce')
  reviews_over_time = df.groupby(df['last_review'].dt.to_period('M')).size()
  reviews_over_time.index = reviews_over_time.index.to_timestamp()
  ```

- **Visualization**:

  ```python
  plt.figure(figsize=(12,6))
  reviews_over_time.plot(kind='line', color='crimson')
  plt.title('Number of Reviews Over Time')
  plt.xlabel('Date')
  plt.ylabel('Number of Reviews')
  plt.xticks(rotation=45)
  plt.tight_layout()
  plt.savefig('images/reviews_over_time.png', bbox_inches='tight')
  plt.show()
  ```

- **Insights**:

  - Identify peaks/dips corresponding to seasonal travel or external events.
  - Spikes may indicate data artifacts; verify data validity.
  - Negative or missing dates handled via `errors='coerce'`; consider imputation or exclusion.

---

## 🔍 Data Cleaning & Preprocessing

1. **Price Column**: Remove currency symbols and commas, convert to float. Handle non-numeric gracefully.
2. **Datetime Conversion**: `pd.to_datetime(..., errors='coerce')` for date columns like `last_review`.
3. **Categorical Normalization**: Standardize text (strip whitespace, unify case) for columns like `neighbourhood_group`, `room type`.
4. **Handling Missing Values**: Drop or impute based on analysis context (e.g., drop rows missing critical fields; fill defaults for optional ones).
5. **Outlier Treatment**: Analyze and decide thresholds for price or review counts; consider trimming extremes for modeling.

---

## 💡 Key Findings & Recommendations

- **Skewed Price Distribution**: Majority of listings cluster at lower prices; outlier handling recommended when modeling.
- **Dominant Room Types**: Entire home/apartment and private rooms dominate; rare types can be grouped or flagged.
- **Geographical Concentration**: High density in core neighbourhood groups; further spatial analysis (mapping) beneficial.
- **Price Differences by Room Type**: Clear median and spread differences; incorporate in pricing or recommendation engines.
- **Temporal Review Trends**: Seasonal patterns and anomalies need interpretation; useful for forecasting and capacity planning.

> 🔮 **Next Steps**:
>
> - **Map-based Visualization**: Plot listings on geographic maps (e.g., Folium, GeoPandas) showing price heatmaps.
> - **Correlation & Feature Analysis**: Explore relationships between price, number of reviews, availability, ratings.
> - **Predictive Modeling**: Build regression or classification models to predict price or occupancy likelihood.
> - **Review Sentiment**: If review text is accessible, perform sentiment analysis for host performance insights.

---

## 📋 Usage

1. Place the Airbnb dataset CSV in `data/listings.csv`.
2. Open and run the notebook: `notebooks/Airbnb_Data_Analysis.ipynb`.
3. Check intermediate outputs, modify code for additional questions.
4. Figures will save under `images/`—include them in presentations or reports.

---

## 📝 Contributing

Contributions are welcome! Feel free to:

- Report issues or suggest enhancements.
- Extend analysis to new datasets or regions.
- Improve documentation or code structure.
- Add advanced visualizations or modeling pipelines.

---
## 💻 Tech Stack  

- **Language:** Python  
- **Libraries:** NumPy, Pandas, Matplotlib, Seaborn, Scikit-learn  
- **Environment:** Jupyter Notebook  

---

## 🏆 About the Author  

👨‍💻 **Laxman Bhimrao Khedkar**  
🎓 Computer Engineering Graduate | Aspiring Data Analyst / Data Scientist  

📧 **Email:** [khedkarlaxman823@gmail.com](mailto:khedkarlaxman823@gmail.com)  
🔗 **Portfolio:** [beacons.ai/laxmankhedkar](https://beacons.ai/laxmankhedkar)  
💼 **LinkedIn:** [linkedin.com/in/laxman-khedkar](https://www.linkedin.com/in/laxman-khedkar)  
🐙 **GitHub:** [github.com/Laxman7744](https://github.com/Laxman7744)  
## ⚖️ License

This project is licensed under the MIT License. See `LICENSE` for details.

---

## 🙏 Acknowledgements

- Airbnb public dataset or Kaggle dataset sources.
- Tutorials and documentation for Pandas, Matplotlib, Seaborn.

---

*Happy Data Exploring!* 🎉  Laxman B Khedkar 

"# Airbnb_Data_Analysis_EDA" 
