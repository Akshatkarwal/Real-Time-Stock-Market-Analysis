# Real-Time Stock Market Data Analysis (PySpark + Databricks)

# 📌 Overview

This project builds a real-time stock market data analysis pipeline using PySpark and Databricks. It fetches live stock data from the Alpha Vantage API, processes it in Databricks using PySpark, and performs trend analysis.

# 🏗️ Tech Stack

Python

PySpark (for distributed data processing)

Databricks (for scalable big data processing)

Pandas & Plotly (for visualization)

Alpha Vantage API (for real-time stock data)

# 🔥 Features

✔ Fetches real-time stock data using Alpha Vantage API
✔ Processes large stock datasets using PySpark in Databricks
✔ Aggregates stock prices over time windows (5 minutes)
✔ Visualizes stock trends using Plotly
✔ Handles missing values for robust analysis

# 📂 Dataset

The dataset is retrieved dynamically from the Alpha Vantage API.

# 🔄 Workflow

Data Ingestion: Fetches stock data from the Alpha Vantage API.

Processing: Uses PySpark to process stock data and compute average closing prices over time windows.

Visualization: Plots real-time stock trends using Plotly.

# 🖥️ Sample Code Snippet

import requests
import pandas as pd
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, avg, window
import plotly.express as px

# Initialize Spark Session
spark = SparkSession.builder.appName("StockMarketPipeline").getOrCreate()

# Fetch stock data
API_KEY = "YOUR_ALPHA_VANTAGE_API_KEY"
STOCK_SYMBOL = "AAPL"
BASE_URL = "https://www.alphavantage.co/query"
params = {"function": "TIME_SERIES_INTRADAY", "symbol": STOCK_SYMBOL, "interval": "5min", "apikey": API_KEY}
response = requests.get(BASE_URL, params=params)
data = response.json()

# Convert to Pandas DataFrame
records = [{"datetime": ts, "close": float(v["4. close"])} for ts, v in data["Time Series (5min)"].items()]
df = pd.DataFrame(records)

# Convert to Spark DataFrame
spark_df = spark.createDataFrame(df)
processed_df = spark_df.groupBy(window(col("datetime"), "5 minutes")).agg(avg("close").alias("avg_close"))

# Convert back to Pandas and plot
pandas_df = processed_df.toPandas()
pandas_df.dropna(subset=["window", "avg_close"], inplace=True)
pandas_df["window"] = pandas_df["window"].astype(str)
fig = px.line(pandas_df, x="window", y="avg_close", title=f"{STOCK_SYMBOL} Stock Price Trends", markers=True)
fig.show()

# 📈 Results

✅ Successfully fetched real-time stock market data.

✅ Processed and aggregated data efficiently with PySpark.

✅ Generated real-time visualizations to track stock trends.

⭐ If you find this project useful, don't forget to star this repository! ⭐
