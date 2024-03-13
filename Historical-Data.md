# RocksDB Historical Data Documentation

## Database:
**Name:** options-historical-Theta

## Data Types:

### EoD Open Interest

The RocksDB database contains key-value pairs, where each key is structured as follows:

```
options-contract;[SYMBOL];[EXPIRATION_DATE];[STRIKE];[RIGHT];[PROPERTY];[PROPERTY_DATE]
```

- `SYMBOL`: The symbol of the options contract.
- `EXPIRATION_DATE`: The expiration date of the options contract.
- `TIMESTAMP`: The timestamp of the data.
- `RIGHT`: Indicates a put or call option.
- `PROPERTY`: The property associated with the options contract.
- `PROPERTY_DATE`: The date associated with the property.

### Example
```cpp
options-contract;SPY;20240628;484000;P;d-eod-open-interest;20231117
```

The corresponding value for each key is a JSON object containing the following fields:

```json
{
  "date": INTEGER,
  "ask": FLOAT,
  "askCondition": INTEGER,
  "askExchange": INTEGER,
  "askSize": INTEGER,
  "bid": FLOAT,
  "bidCondition": INTEGER,
  "bidExchange": INTEGER,
  "bidSize": INTEGER,
  "close": FLOAT,
  "count": INTEGER,
  "high": FLOAT,
  "low": FLOAT,
  "open": FLOAT,
  "volume": INTEGER,
  "openInterest": INTEGER
}
```

- `date`: The date of the current data.
- `ask`: The asking price.
- `askCondition`: Condition of the ask.
- `askExchange`: Exchange for the ask.
- `askSize`: Size of the ask.
- `bid`: The bidding price.
- `bidCondition`: Condition of the bid.
- `bidExchange`: Exchange for the bid.
- `bidSize`: Size of the bid.
- `close`: Closing price.
- `count`: Number of data points.
- `high`: Highest price.
- `low`: Lowest price.
- `open`: Opening price.
- `volume`: Trading volume.
- `openInterest`: Open interest.

## Example Python Code to Load and Parse the Database

```python
import rocksdb
import json

def load_rocksdb_database(db_path):
    db = rocksdb.DB(db_path, rocksdb.Options(read_only=True))
    data = {}
    for key, value in db:
        key = key.decode('utf-8')
        value = json.loads(value.decode('utf-8'))
        data[key] = value
    return data

# Example usage
db_path = "/home/myname/googleDrive/options-historical-Theta"
data = load_rocksdb_database(db_path)

# Accessing parsed data
for key, value in data.items():
    print("Key:", key)
    print("Parsed Data:")
    print(json.dumps(value, indent=2))  # Print the parsed data with indentation
```
