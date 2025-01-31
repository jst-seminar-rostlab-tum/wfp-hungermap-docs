# CSV utils

**Author:** `Marius Moldovan`

The **CSV Utils** Module provides an easy adn efficient way to interact with CSV files.

## Overview
1. **`read_csv_data`**: Reads data from a CSV file and returns it as a list of dictionaries


## Classes and Functions

### Function: `read_csv_data(file_path)`

Read data from a CSV file and return a list of dictionaries.

*Params*:
- **file_path** (`str`): Path to the CSV file.

*Returns*:
- `List[Dict[str, str]]:`: List of dictionaries containing the CSV data. Each element of the list contains the data of one row of the CSV file.

*Behavior*:
1. Opens the CSV file in read mode with UTF-8 encoding.
2. Uses `csv.DictReader` to read the csv file content and map every row to a dictionary
3. Appends each dictionary to a list, which is then returned

*Example Usage*:
```python
from csv_utils import read_csv_data

file_path = "example.csv"
data = read_csv_data(file_path)

for row in data:
    print(row)
```

