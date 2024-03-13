## Database Documentation

- [Options Historical Database](https://github.com/RealTimeChris/Docs/blob/main/Options-Historical-Data.md)    
- [Stocks Historical Database](https://github.com/RealTimeChris/Docs/blob/main/Stocks-Historical-Data.md)    

### Overview

This document provides comprehensive documentation for the data stored in the RocksDB databases. The database primarily contains options contracts and associated data types.

### Database Structure

The data is stored in distinct databases, each following a specific naming convention:

- `/home/myname/googleDrive/[YYYYMMDD]-options-IB`: Database dedicated to storing options contracts data using the IB API.
- `/home/myname/googleDrive/[YYYYMMDD]-options-Theta`: Database designed for storing options contracts data using the Theta API.

### Storage Keys

#### Contract Storage Key Format

The storage keys for options contracts adhere to the following format:

```
options-contract;[Symbol];[ExpiryDate];[StrikePrice];[Right];[DataTypeName]
```

For the IB database, an additional element, `[ContractID]`, is included in the key:

```
options-contract;[Symbol];[ExpiryDate];[StrikePrice];[Right];[ContractID];[DataTypeName]
```

- `[Symbol]`: Symbol of the underlying asset.
- `[ExpiryDate]`: Expiration date of the options contract (YYYYMMDD format).
- `[StrikePrice]`: Strike price of the options contract.
- `[Right]`: Type of option (C for Call, P for Put).
- `[ContractID]`: ID of the contract in the IB database.
- `[DataTypeName]`: Name of the associated data type.

Example:
```
options-contract;SPY;20240628;560;C;642254105;d-open-interest;0;1710577002
```

### Data Parsing

To parse the stored data, you can directly extract the information from the storage keys. Each key contains all the necessary details of the options contract and its associated data type.

The data stored after the storage key represents an array of the values of the associated data type. In the provided example, the value is `0;1710577002`, where `0` is a float value and `1710577002` is a uint64_t value, representing the time in seconds since the Linux epoch that the associated data value was collected.

### Example Usage

Below is an example of how to retrieve and parse data from the databases:

```python
import rocksdb
import json

# Open the RocksDB database in read-only mode
db = rocksdb.DB("/home/myname/googleDrive/20240628-options-IB", rocksdb.Options(read_only=True))

# Define the contract key
contract_key = b'options-contract;SPY;20240628;560;C;642254105;d-open-interest;'

# Get the contract value from the database
contract_value = db.get(contract_key)

# Convert the contract value from bytes to string
contract_value_str = contract_value.decode('utf-8')

# Remove the leading and trailing whitespace and split the values by comma
datestamped_values = contract_value_str.strip().split(',')

# Define a list to store parsed datestamped values
parsed_datestamped_values = []

# Parse each datestamped value
for datestamped_value in datestamped_values:
    value, timestamp = datestamped_value.strip().split(';')
    parsed_datestamped_values.append({'value': float(value), 'timestamp': int(timestamp)})

# Print contract key
print("Contract Key:")
print("Key:", contract_key.decode('utf-8'))

# Print parsed datestamped values
print("Parsed Datestamped Values:")
for ds_value in parsed_datestamped_values:
    print("Value:", ds_value['value'], "Timestamp:", ds_value['timestamp'])

# Close the RocksDB database
db.close()
```

Ensure that the correct database paths are used and that the provided code snippets are adapted accordingly.

---

Please replace `[Symbol]`, `[ExpiryDate]`, `[StrikePrice]`, `[Right]`, `[ContractID]`, and `[DataTypeName]` with the actual values from your data. Additionally, ensure that your parsing methods handle the associated data values correctly.

Got it! Here's the corrected section of documentation for loading data from a RocksDB database named "YYYYMMDD-options-historical-Theta" with the specified key and value:

---

## Loading Open-Interest-Historical values from RocksDB Database "options-historical-Theta"

To load data from a RocksDB database named "options-historical-Theta," follow the steps outlined below:

### 1. Initialize RocksDB Connection

First, ensure you have the necessary Python RocksDB library installed. If not, you can install it using pip:

```bash
pip install python-rocksdb
```

Next, import the required modules and initialize the RocksDB connection:

```python
import rocksdb

# Path to the RocksDB database directory
db_path = "/home/myname/googleDrive/options-historical-Theta"

# Open the RocksDB database in read-only mode
db = rocksdb.DB(db_path, rocksdb.Options(read_only=True))
```

### 2. Load Data

With the RocksDB connection established, you can now load data from the database using the specified key:

```python
key = "options-contract;SPY;20240315;490000;C;d-open-interest-historical;20230726"

# Retrieve data from RocksDB
try:
    value = db.get(key.encode('utf-8')).decode('utf-8')
    print("Data loaded successfully:")
    print(value)
except rocksdb.errors.NotFound:
    print("Data not found for the specified key.")
```

### 3. Close RocksDB Connection

Once you've finished accessing the data, remember to close the RocksDB connection to release system resources:

```python
# Close the RocksDB connection
db.close()
```

That's it! You've successfully loaded data from the RocksDB database "options-historical-Theta" using a read-only access type in Python.

--- 

This section provides clear instructions for users on how to load data from a specific RocksDB database with the given key and value using Python.