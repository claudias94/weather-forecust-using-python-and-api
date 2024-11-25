# weather-forecust-using-python-and-api
# A Python script that provides real-time weather forecasts for towns across Kenya using the OpenWeatherMap API.
import requests
from datetime import datetime, timedelta

# Weather Forecast Script for Kenyan Towns
# Author: Claudias Musavini Misiko
# Date: 30th November 2024
# Description: This script provides real-time weather forecasts for selected towns in Kenya 
# using the OpenWeatherMap API. Users can choose from a predefined list of towns.

# Predefined list of Kenyan towns
kenyan_towns = [
    "Nairobi", "Mombasa", "Kisumu", "Eldoret", "Kitale", "Nakuru", "Thika", "Nyeri",
    "Garissa", "Isiolo", "Machakos", "Meru", "Naivasha", "Kericho", "Kakamega", 
    "Lamu", "Malindi", "Busia", "Embu", "Narok"
]

# Function to display the list of Kenyan towns
def display_town_list():
    print("Available Kenyan Towns for Weather Forecast:")
    print("-" * 50)
    for town in kenyan_towns:
        print(town)
    print("-" * 50)

# Function to fetch and display weather forecast
def get_weather_forecast(town):
    # API key from environment variable (replace with your API key if needed)
    user_api = os.getenv('current_weather_data', 'your_default_api_key_here')
    api_url = f"https://api.openweathermap.org/data/2.5/forecast?q={town},Kenya&appid={user_api}"
    
    try:
        # Fetch data from the API
        response = requests.get(api_url)
        response.raise_for_status()  # Raise HTTPError for bad responses
        data = response.json()

        # Check if the API returned an error
        if data.get('cod') != "200":
            print(f"Error: {data.get('message', 'Unable to fetch forecast.')}")
            return

        # Display forecast details
        print(f"\nWeather Forecast for {town.upper()}, Kenya")
        print("-" * 50)
        for forecast in data['list']:
            # Convert timestamp to readable date and time
            forecast_time = datetime.utcfromtimestamp(forecast['dt']) + timedelta(hours=3)  # Adjust for local time
            forecast_date = forecast_time.strftime("%d %b %Y | %I:%M %p")
            
            # Extract weather details
            temp = forecast['main']['temp'] - 273.15  # Kelvin to Celsius
            weather_desc = forecast['weather'][0]['description']
            humidity = forecast['main']['humidity']
            wind_speed = forecast['wind']['speed'] * 3.6  # Convert m/s to km/h
            
            # Display forecast
            print(f"{forecast_date}")
            print(f"Temperature: {temp:.2f}°C, Weather: {weather_desc}")
            print(f"Humidity: {humidity}%, Wind Speed: {wind_speed:.2f} km/h")
            print("-" * 50)
    except requests.exceptions.RequestException as e:
        print(f"Error: Unable to connect to the weather service. Details: {e}")

# Main program
if __name__ == "__main__":
    print("Welcome to the Kenyan Town Weather Forecast Tool!")
    display_town_list()
    
    # Prompt user to select a town
    selected_town = input("Enter the name of a town from the list above: ").strip()
    
    if selected_town in kenyan_towns:
        get_weather_forecast(selected_town)
    else:
        print("Error: The town you entered is not on the list. Please try again.")
