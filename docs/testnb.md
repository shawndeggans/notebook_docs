# Import cleaning process

The following notebook is part of our import cleaning process.
This notebook accomplishes the following:
- Imports a CSV file
- Removes extra columns
- Converts strings to correct data types
- Saves in the cleansed directory


```python
# Pip install
%pip install pandas

# Here we would import libraries
import sys
import pandas as pd

```

## Import file from raw data folder
Here we import the file from the raw folder


```python
# Pseudo code for opening a file, importing a CSV, and loading it into pandas

# Define the file path
file_path = 'path/to/your/csvfile.csv'

# Use pandas to read the CSV file
df = pd.read_csv(file_path)

# Display the first few rows of the dataframe
print(df.head())
```

## Removes extra columns
Removing the address and phone fields


```python
# BEGIN: Remove extra columns

# List of columns to remove
columns_to_remove = ['address', 'phone']

# Remove the specified columns
df_cleaned = df.drop(columns=columns_to_remove)

# Display the first few rows of the cleaned dataframe
print(df_cleaned.head())

# END: Remove extra columns
```

## Set data types
Sets the correct datatypes for date and identity fields.


```python
# Pseudo code for setting data types

# Convert the 'date_field' to datetime
df_cleaned['date_field'] = pd.to_datetime(df_cleaned['date_field'])

# Convert the 'identity_field' to numeric (integer)
df_cleaned['identity_field'] = pd.to_numeric(df_cleaned['identity_field'], errors='coerce')

# Display the data types of the dataframe to verify changes
print(df_cleaned.dtypes)
```

## Save new data to a cleansed directory
Write the cleansed data from Pandas to a new CSV file in the cleansed folder


```python
# Pseudo code to write the cleansed data to a new CSV file in the cleansed folder

# Define the output file path
output_file_path = 'path/to/cleansed/folder/cleansed_data.csv'

# Use pandas to write the dataframe to a CSV file
df_cleaned.to_csv(output_file_path, index=False)

# Confirm the file has been written
print(f"Cleansed data has been written to {output_file_path}")
```
