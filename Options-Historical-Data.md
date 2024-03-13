# Loading Options Database Data

## Introduction

This document provides instructions on how to load data from the "options-historical-Theta" database using Python. The database contains historical options data in a specific format, and we'll cover how to parse and process this data.

## Data Format

The data in the "options-historical-Theta" database is stored in key-value pairs, where each key has the following format:

```
options-contract;[ROOT];[EXPIRATION];[STRIKE];[RIGHT];[DATA-TYPE]
```

Here, `[ROOT]`, `[EXPIRATION]`, `[STRIKE]`, `[RIGHT]`, and `[DATA-TYPE]` are placeholders for actual values representing the root symbol, expiration date, strike price, right (call/put), and data type, respectively.

### Data Types

The database contains data of different types, including:

- **Open Interest**: Provides information about open interest for each options contract.
- **End-of-Day (EOD)**: Contains end-of-day data such as timestamp, volume, open, high, low, close prices, bid, and ask prices.
- **Quote**: Includes quote-related data such as ask conditions, bid conditions, bid/ask exchange details, bid/ask sizes, volume, count, close/open/high/low prices, bid, and ask prices.

Detailed formats for each data type are provided below.

#### Open Interest

```json
{
    "root": "[ROOT]",
    "data": {
        "date": [20230314, 20230315, ...],
        "msOfDay": [23401389, 23402242, ...],
        "openInterest": [0, 0, ...]
    }
}
```

#### End-of-Day (EOD)

```json
{
    "root": "[ROOT]",
    "data": {
        "msOfDay": [34200000, 37800000, ...],
        "date": [20230313, 20230313, ...],
        "askCondition": [50, 50, ...],
        "bidCondition": [50, 50, ...],
        "bidExchange": [1, 42, ...],
        "askExchange": [1, 42, ...],
        "askSize": [0, 7, ...],
        "bidSize": [0, 7, ...],
        "volume": [1, 29, ...],
        "count": [1, 12, ...],
        "close": [87.3, 87.26, ...],
        "open": [87.3, 88.7, ...],
        "high": [87.3, 91.54, ...],
        "low": [87.3, 86.96, ...],
        "bid": [0, 0, ...],
        "ask": [0, 0, ...]
    }
}
```

#### Quote

```json
{
    "root": "[ROOT]",
    "data": {
        "msOfDay": [34200000, 37800000, ...],
        "date": [20230313, 20230313, ...],
        "askCondition": [50, 50, ...],
        "bidCondition": [50, 50, ...],
        "bidExchange": [1, 42, ...],
        "askExchange": [1, 42, ...],
        "askSize": [0, 7, ...],
        "bidSize": [0, 7, ...],
        "volume": [1, 29, ...],
        "count": [1, 12, ...],
        "close": [87.3, 87.26, ...],
        "open": [87.3, 88.7, ...],
        "high": [87.3, 91.54, ...],
        "low": [87.3, 86.96, ...],
        "bid": [0, 0, ...],
        "ask": [0, 0, ...]
    }
}
```

## Loading Data

To load data from the "options-historical-Theta" database, you can use Python and RocksDB. Here's an example code snippet:

```python
import rocksdb
import json

# Open the RocksDB database
db = rocksdb.DB("path/to/options-historical-Theta", rocksdb.Options(read_only=True))

def load_options

_data(root, expiration, strike, right, data_type):
    key = f"options-contract;{root};{expiration};{strike};{right};{data_type}".encode("utf-8")
    data = db.get(key)
    if data is not None:
        return json.loads(data.decode("utf-8"))
    else:
        return None

# Example usage
root = "SPY"
expiration = "20230322"
strike = "310000"
right = "C"
data_type = "open-interest"
options_data = load_options_data(root, expiration, strike, right, data_type)
if options_data:
    print("Data found:", options_data)
else:
    print("No data found for the specified parameters.")
```

Replace `"path/to/options-historical-Theta"` with the actual path to your RocksDB database.

## Loading Data - Iterating Over The Data

```python
import rocksdb
import json

# Open the RocksDB database
db = rocksdb.DB("path/to/options-historical-Theta", rocksdb.Options(read_only=True))

def iterate_options_data(root, right=None):
    # Construct prefix for the specified ticker
    key_prefix = f"options-contract;{root};"
    
    # List to store parsed data
    parsed_data = []
    
    # Iterate over each key-value pair in the database
    for key, value in db.iterator(prefix=key_prefix.encode()):
        # Decode key and value from bytes to string
        key_str = key.decode("utf-8")
        value_str = value.decode("utf-8")
        
        # Parse key to extract expiration and right
        key_parts = key_str.split(";")
        expiration = key_parts[2]
        option_right = key_parts[4]
        
        # Check if the right matches the specified option type (call, put, or both) or if no option type is specified
        if right is None or option_right == right:
            # Parse value based on data type
            data = json.loads(value_str)
            parsed_data.append({
                "root": root,
                "expiration": expiration,
                "right": option_right,
                "data": data
            })
    
    return parsed_data

# Example usage: Iterate over all call options for ticker "SPY"
call_options_data = iterate_options_data("SPY", right="C")
print(call_options_data)
```