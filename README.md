import requests

# Make the initial API call to get city names and condition
initial_api_url = "https://quest.squadcast.tech/api/2020105555/weather"
response = requests.get(initial_api_url)
data = response.json()

if "condition" not in data:
    print("Invalid API response. Missing 'condition'.")
else:
    condition = data["condition"]

    # Make API calls to get weather details for each city
    cities = data.get("cities", [])
    better_location = None

    for city in cities:
        city_name = city.get("name")

        # Make API call to get weather details for the current city
        city_weather_url = f"https://quest.squadcast.tech/api/2020105555/weather/get?q={city_name}"
        weather_response = requests.get(city_weather_url)
        weather_data = weather_response.json()

        # Evaluate the condition and update better_location accordingly
        if condition == "hot" and "temperature" in weather_data:
            if not better_location or weather_data["temperature"] > better_location["temperature"]:
                better_location = {"city": city_name, "temperature": weather_data["temperature"]}
        elif condition == "cold" and "temperature" in weather_data:
            if not better_location or weather_data["temperature"] < better_location["temperature"]:
                better_location = {"city": city_name, "temperature": weather_data["temperature"]}
        elif condition == "windy" and "wind" in weather_data:
            if not better_location or weather_data["wind"] > better_location["wind"]:
                better_location = {"city": city_name, "wind": weather_data["wind"]}
        elif condition == "rainy" and "rain" in weather_data:
            if not better_location or weather_data["rain"] > better_location["rain"]:
                better_location = {"city": city_name, "rain": weather_data["rain"]}
        elif condition == "sunny" and "cloud" in weather_data:
            if not better_location or weather_data["cloud"] < better_location["cloud"]:
                better_location = {"city": city_name, "cloud": weather_data["cloud"]}
        elif condition == "cloudy" and "cloud" in weather_data:
            if not better_location or weather_data["cloud"] > better_location["cloud"]:
                better_location = {"city": city_name, "cloud": weather_data["cloud"]}

    if better_location:
        print(f"The better location based on the condition '{condition}' is {better_location['city']}.")
    else:
        print("No suitable location found based on the given condition.")
