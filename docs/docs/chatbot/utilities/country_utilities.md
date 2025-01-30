# Country utilities

**Author:** `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of the **Country Utilities** module, which includes functions for fetching country names based on country IDs and retrieving all available country IDs. It uses `pandas` for data manipulation and assumes the country data is provided in a CSV file.

---

## Overview

The **Country Utilities** module provides:
- A method to fetch country names by their ID.
- A method to retrieve a list of all available country IDs from the data source.
- Integration with a CSV file containing country data.

---

## Requirements and Setup

Before using this module, ensure you have the following:
- Python 3.8+
- The `pandas` library installed.
- A CSV file containing country data in the specified directory structure.

### Directory Structure

The module assumes the following directory structure:
```plaintext
project_root/
├── assets/
│   ├── country_data/
│   │   ├── country_ids.csv
```

### CSV File Format

The `country_ids.csv` file must contain the following columns:
- `Country ID`: A unique identifier for each country.
- `Country Name`: The name of the country.

Example `country_ids.csv`:
```plaintext
Country ID,Country Name
1,United States
2,Canada
3,Mexico
```

---



## Key Features

#### Fetching Country Names
Retrieve a country name by providing its corresponding ID.

#### Listing Country IDs
Retrieve a list of all available country IDs from the CSV file.

---

## Functions

**Function**: `get_country_name(country_id)`

Fetches the name of a country based on its ID.

*Arguments*:
- `country_id` (str or int): The ID of the country.

Returns:
- `str`: The name of the country if found; otherwise, `"N/A"`.

Example:
```python
country_name = get_country_name(1)
print(country_name)  # Output: United States
```

---

**Function:** `get_list_of_all_country_ids()`

Retrieves a list of all country IDs available in the CSV file.

*Arguments*:
- `list`: A list of all country IDs.

Example:
```python
country_ids = get_list_of_all_country_ids()
print(country_ids)  # Output: [1, 2, 3]
```

---

### Example Usage

Here is an example demonstrating how to use the module:
```python
from country_utils import get_country_name, get_list_of_all_country_ids

# Get the list of country IDs
country_ids = get_list_of_all_country_ids()

# Print the name of each country
for country_id in country_ids:
    print(f"Country ID: {country_id}, Country Name: {get_country_name(country_id)}")
```

---

## What Happens Under the Hood?

1. **Data Loading:** The `pandas` library reads the `country_ids.csv` file into a DataFrame when the script is executed.
2. **Country Name Lookup:** The `get_country_name` function uses `pandas` filtering to find the row corresponding to the provided `country_id`.
3. **Country ID Listing:** The `get_list_of_all_country_ids` function extracts the `Country ID` column from the DataFrame and converts it to a list.

---

## Conclusion

The **Country Utilities** module simplifies operations involving country data, such as:
- Fetching country names based on IDs.
- Retrieving all available country IDs.
