# Location utilities

**Author:** `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of the **Location Utilities** module, which includes utility functions for working with location data. The module leverages OpenStreetMap (via `geopy`), `pycountry` for country data, and OpenAI GPT models for enhanced string-based location detection.

---

## Overview

The **Location Utilities** module provides:
- Reverse geocoding to fetch country and city names from latitude and longitude.
- Identification of country names from a string, with optional GPT integration for advanced predictions.
- Conversion of ISO country codes to their full names.
- Comprehensive logging for monitoring and debugging.

---

## Requirements and Setup

Before using this module, ensure you have the following:
- Python 3.8+
- A valid **OpenAI API Key** for GPT-based operations (optional).
- Installed dependencies from `requirements.txt`.

### Environment Variables

Set up the following environment variables in your system or in a `.env` file:
- **OPENAI_API_KEY**: Your API key for OpenAI GPT.

Example `.env` file:
```plaintext
OPENAI_API_KEY=your_openai_api_key
```

---
## Key Features

#### Reverse Geocoding

The module uses the geopy library to fetch country and city names based on geographical coordinates.

#### Naive and GPT-Based Location Detection

Identifies country names in strings using:
	•	A naive word-matching method based on pycountry.
	•	An advanced GPT-based method for better accuracy (optional).

#### ISO Country Code Conversion

Converts two-letter ISO country codes into their full country names using pycountry.

## Classes and Functions

**Function**:  `get_country_and_city(lat, long)`

Retrieves the country code and city name for the provided latitude and longitude.

*Arguments*:
- `lat` (float): Latitude of the location.
- `long` (float): Longitude of the location.

Returns:
- A tuple (country_code, city_name) or (None, None) if an error occurs.

Example:

```python
country, city = get_country_and_city(51.5074, -0.1278)
print(country, city)  # Output: GB London
```
---
**Function**: `get_country_from_string(random_string, use_gpt=False, skip_naive=False)`

Identifies a country name from a string. Supports naive detection or GPT-4 for enhanced predictions.

*Arguments*:
-	`random_string` (str): The input string to analyze.
-	`use_gpt` (bool): Whether to use GPT for detection. Defaults to False.
-	`skip_naive` (bool): If True, skips naive detection. Defaults to False.

Returns:
- The detected country name or None if no match is found.

Example:
```python
country = get_country_from_string("Traveling to DE soon.", use_gpt=False)
print(country)  # Output: Germany
```
---
**Function:** `convert_country_code_to_name**(country_code)`

Converts an ISO country code into its full name.

*Arguments*:
- `country_code` (str): The two-letter ISO country code.

Returns:
- The country name or None if the code is invalid.

Example:
```python
country_name = convert_country_code_to_name("US")
print(country_name)  # Output: United States
```
---
### Example Usage

Here is an example demonstrating how to use the module:
```python
from loc_utils import get_country_and_city, get_country_from_string, convert_country_code_to_name

# Reverse geocoding
country, city = get_country_and_city(40.7128, -74.0060)
print(f"Country: {country}, City: {city}")

# String-based country detection
country = get_country_from_string("I want to visit Canada", use_gpt=False)
print(f"Detected Country: {country}")

# Country code conversion
country_name = convert_country_code_to_name("FR")
print(f"Country Name: {country_name}")
```
---
## What Happens Under the Hood?
1.	Reverse Geocoding: The get_country_and_city function uses the geopy library to fetch location data from OpenStreetMap.
2.	Naive Detection: The get_country_from_string function uses pycountry to match words in the string against country names and codes.
3.	GPT Detection: When enabled, the function sends a prompt to GPT-4 for advanced predictions.
4.	Error Handling: Comprehensive exception handling ensures robustness against invalid inputs and network errors.
---
## Conclusion

The Location Utilities module simplifies location data processing with:
- Fast and accurate geocoding using OpenStreetMap.
- Flexible country detection with naive and GPT-based options.
- Easy conversion between ISO country codes and names.

