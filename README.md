import requests
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import datetime

# Replace with your OpenWeatherMap API Key
API_KEY = "your_api_key"

# Function to fetch weather data
def fetch_weather_data(city_name):
    """
    Fetch 5-day weather forecast data from OpenWeatherMap API.
    """
    url = f"http://api.openweathermap.org/data/2.5/forecast?q={city_name}&appid={API_KEY}&units=metric"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to fetch data for {city_name}. HTTP Status: {response.status_code}")

# Process weather data into a DataFrame
def process_weather_data(data):
    """
    Process JSON weather data and return a DataFrame.
    """
    weather_data = {
        "datetime": [],
        "temperature": [],
        "humidity": [],
        "weather": []
    }

    for entry in data["list"]:
        weather_data["datetime"].append(datetime.datetime.fromtimestamp(entry["dt"]))
        weather_data["temperature"].append(entry["main"]["temp"])
        weather_data["humidity"].append(entry["main"]["humidity"])
        weather_data["weather"].append(entry["weather"][0]["description"])

    return pd.DataFrame(weather_data)

# Visualization
def visualize_weather_data(data, city_name):
    """
    Create visualizations for temperature and humidity trends.
    """
    sns.set(style="whitegrid")

    # Temperature Trend
    plt.figure(figsize=(10, 6))
    sns.lineplot(x="datetime", y="temperature", data=data, marker="o", color="blue")
    plt.title(f"Temperature Trend in {city_name}", fontsize=16)
    plt.xlabel("Datetime", fontsize=12)
    plt.ylabel("Temperature (°C)", fontsize=12)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

    # Humidity Trend
    plt.figure(figsize=(10, 6))
    sns.lineplot(x="datetime", y="humidity", data=data, marker="o", color="green")
    plt.title(f"Humidity Trend in {city_name}", fontsize=16)
    plt.xlabel("Datetime", fontsize=12)
    plt.ylabel("Humidity (%)", fontsize=12)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# Main Function
if __name__ == "__main__":
    city = input("Enter the city name: ")

    try:
        # Fetch and process data
        raw_data = fetch_weather_data(city)
        weather_df = process_weather_data(raw_data)

        # Display DataFrame
        print(weather_df.head())

        # Visualize data
        visualize_weather_data(weather_df, city)

    except Exception as e:
        print(f"Error: {e}")
