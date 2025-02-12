# File Format Converter - Automating the conversion of multiple CSV files into JSON format using python


## Overview

The objective of this project is to develop solutions based on the design provided. In this case, the source data was obtained in the form of CSV files from a MySQL DB.

To improve the efficiency of our data engineering pipelines, we need to convert these CSV files into JSON files, since JSON is better to use in downstream applications than CSV files. The scope of this project involves converting CSV files into JSON files.

## Data Model Details
![Data_Model_Details](https://github.com/Chetanchandra1994/Project-1---File-Format-Converter/assets/71788058/d1d479b2-0aa2-4360-87ca-2d778d3d32eb)


## Design

![Design](https://github.com/Chetanchandra1994/Project-1---File-Format-Converter/assets/71788058/7816574b-7d42-4a74-b8d3-cca2d1b64877)

 
### Setup Instructions
1.	Setup the Project Using VSCode
2.	Make sure you have set up a virtual environment (creating venv, requirements.txt, etc.,) and installed dependencies for the project.
3.	It is essential that you deploy the application with the core logic.
4.	Run the project after setting all the environment variables.
5.	Take appropriate steps to handle the exception

### Validation Steps
1.	You should check whether the data in the files has been converted properly.
2.	Make sure the target folder has been created and populated with JSON files and confirm that the schema structure was accurately reflected from the CSV file. (Hint: Refer to schemas.json)
3.	Take the count of records in the CSV files and compare it to the number of records in the JSON files.


Technologies Used
-	Programming Language – Python
-	Pandas – For Converting CSV to Dataframe and then Dataframe into JSON.

```

import glob
import os
import json
import re
import pandas as pd

def get_column_names(schemas, ds_name, sorting_key='column_position'):
    column_details = schemas[ds_name]
    columns = sorted(column_details, key=lambda col: col[sorting_key])
    return [col['column_name'] for col in columns]

def read_csv(file, schemas):
    file_path_list = re.split('[/\\\]', file)
    ds_name = file_path_list[-2]
    file_name = file_path_list[-1]
    columns = get_column_names(schemas, ds_name)
    df = pd.read_csv(file, names=columns)
    return df

def to_json(df, tgt_base_dir, ds_name, file_name):
    json_file_path = f'{tgt_base_dir}/{ds_name}/{file_name}'
    os.makedirs(f'{tgt_base_dir}/{ds_name}', exist_ok=True)
    df.to_json(
        json_file_path,
        orient='records',
        lines=True
    )

def file_converter(src_base_dir, tgt_base_dir, ds_name):
    schemas = json.load(open(f'{src_base_dir}/schemas.json'))
    files = glob.glob(f'{src_base_dir}/{ds_name}/part-*')

    for file in files:
        df = read_csv(file, schemas)
        file_name = re.split('[/\\\]', file)[-1]
        to_json(df, tgt_base_dir, ds_name, file_name)

def process_files(ds_names=None):
    src_base_dir = 'data/retail_db'
    tgt_base_dir = 'data/retail_db_json'
    schemas = json.load(open(f'{src_base_dir}/schemas.json'))
    if not ds_names:
        ds_names = schemas.keys()
    for ds_name in ds_names:
        print(f'Processing {ds_name}')
        file_converter(src_base_dir, tgt_base_dir, ds_name)

process_files()

schemas = json.load(open('data/retail_db/schemas.json'))

schemas.keys()
```

## Output:


![import](https://github.com/Chetanchandra1994/Project-1---File-Format-Converter/assets/71788058/0780b24b-7993-43c3-a3a1-14e1406a791a)


![functions](https://github.com/Chetanchandra1994/Project-1---File-Format-Converter/assets/71788058/b2f827b7-7f6f-40e4-97f9-e23474e3c506)


![output](https://github.com/Chetanchandra1994/Project-1---File-Format-Converter/assets/71788058/0696cd20-32f6-4343-ab27-0107bcce1319)



## Overview of the Code Structure:
The code is designed to convert multiple CSV files into JSON format using predefined schemas. Here's what each function and part of the code does:
1. get_column_names Function:
 ```  
def get_column_names(schemas, ds_name, sorting_key='column_position'):
    column_details = schemas[ds_name]
    columns = sorted(column_details, key=lambda col: col[sorting_key])
    return [col['column_name'] for col in columns]
```    
- Purpose: Retrieves column names from a schema based on the dataset name (ds_name).
- Parameters:
  1. schemas: A dictionary containing schemas loaded from schemas.json.
  2. ds_name: Name of the dataset for which column names are retrieved.
  3. sorting_key: Optional key to specify sorting criteria (default is 'column_position').
     
- Explanation:
  1. schemas[ds_name]: Accesses the schema details for the dataset specified by ds_name.
  2. sorted(column_details, key=lambda col: col[sorting_key]): Sorts the columns based on column_position (or another specified key).
  3. Returns a list of column names extracted from the sorted schema details.

2. read_csv Function:
```
def read_csv(file, schemas):
    file_path_list = re.split('[/\\\]', file)
    ds_name = file_path_list[-2]
    file_name = file_path_list[-1]
    columns = get_column_names(schemas, ds_name)
    df = pd.read_csv(file, names=columns)
    return df
  ```  
- Purpose: Reads a CSV file into a pandas DataFrame using column names from the schema.
- Parameters:
  1. file: File path of the CSV file to read.
  2. schemas: Dictionary containing schemas loaded from schemas.json.
     
- Explanation:
  1. re.split('[/\\\]', file): Splits the file path into components using either / or \ as the separator.
  2. ds_name = file_path_list[-2]: Extracts the dataset name from the file path.
  3. file_name = file_path_list[-1]: Extracts the file name from the file path.
  4. columns = get_column_names(schemas, ds_name): Retrieves column names for the dataset using get_column_names function.
  5. pd.read_csv(file, names=columns): Reads the CSV file into a DataFrame (df) using the retrieved column names.


3. to_json Function:
```
def to_json(df, tgt_base_dir, ds_name, file_name):
    json_file_path = f'{tgt_base_dir}/{ds_name}/{file_name}'
    os.makedirs(f'{tgt_base_dir}/{ds_name}', exist_ok=True)
    df.to_json(
        json_file_path,
        orient='records',
        lines=True
    )
 ```   
- Purpose: Converts a pandas DataFrame to JSON and saves it to a file.
- Parameters:
  1. df: Pandas DataFrame to convert to JSON.
  2. tgt_base_dir: Base directory where JSON files will be saved.
  3. ds_name: Dataset name for organizing JSON files.
  4. file_name: Name of the JSON file to be created.

- Explanation:
  1. json_file_path = f'{tgt_base_dir}/{ds_name}/{file_name}': Constructs the full path for the JSON file.
  2. os.makedirs(f'{tgt_base_dir}/{ds_name}', exist_ok=True): Creates the directory (tgt_base_dir/ds_name) if it doesn't exist.
  3. df.to_json(...): Converts DataFrame df to JSON format and saves it to json_file_path.
  4. orient='records': Each DataFrame row is saved as a separate JSON object.
  5. lines=True: JSON objects are written as lines in the file.

4. file_converter Function:
```
def file_converter(src_base_dir, tgt_base_dir, ds_name):
    schemas = json.load(open(f'{src_base_dir}/schemas.json'))
    files = glob.glob(f'{src_base_dir}/{ds_name}/part-*')

    for file in files:
        df = read_csv(file, schemas)
        file_name = re.split('[/\\\]', file)[-1]
        to_json(df, tgt_base_dir, ds_name, file_name)
```
- Purpose: Converts all CSV files in a dataset directory to JSON using specified schemas.
- Parameters:
  1. src_base_dir: Base directory where CSV files and schemas are located.
  2. tgt_base_dir: Base directory where JSON files will be saved.
  3. ds_name: Dataset name to process.
  
- Explanation:
  1. Loads schemas from schemas.json for the dataset.
  2. glob.glob(f'{src_base_dir}/{ds_name}/part-*'): Finds all CSV files (part-*) in the dataset directory.
  3. Iterates over each CSV file found (file), reads it using read_csv, and converts it to JSON using to_json.

5. process_files Function:
```
def process_files(ds_names=None):
    src_base_dir = 'data/retail_db'
    tgt_base_dir = 'data/retail_db_json'
    schemas = json.load(open(f'{src_base_dir}/schemas.json'))
    if not ds_names:
        ds_names = schemas.keys()
    for ds_name in ds_names:
        print(f'Processing {ds_name}')
        file_converter(src_base_dir, tgt_base_dir, ds_name)
```
-	Purpose: Orchestrates the conversion process for multiple datasets.
-	Parameters:
  1. ds_names: Optional list of dataset names to process. If None, processes all datasets in schemas.
 	
- Explanation:
  1. Loads schemas from schemas.json.
  2. If ds_names is not provided, uses all dataset names from schemas.
  3. Iterates over each dataset name (ds_name), prints a processing message, and calls file_converter to convert CSV files to JSON for each dataset.

## Summary:
The above steps automates the conversion of multiple CSV files into JSON format based on predefined schemas (schemas.json). It handles file reading, schema-based column naming, conversion to JSON, and directory creation for output files. The process_files function serves as the entry point, orchestrating the conversion process for all datasets defined in schemas.json.

