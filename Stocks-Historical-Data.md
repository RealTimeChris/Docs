# RocksDB Historical Data Documentation

## Database:
**Name:** stocks-historical-Theta

## Data Types:

### EoD Open Interest

The RocksDB database contains key-value pairs, where each key is structured as follows:

```
[SYMBOL];d-stock-historical-eod;[DATE];
```

- `SYMBOL`: The symbol of the stock.
- `DATE`: The date of the snapshot.

### Example
```cpp
SPY;d-stock-historical-eod;20230601;
```

The corresponding value for each key is a JSON object containing the following fields:

```json
{
  "data": [
    {
      "date": INTEGER,
      "askCondition": INTEGER,
      "bidCondition": INTEGER,
      "openInterest": INTEGER,
      "bidExchange": INTEGER,
      "askExchange": INTEGER,
      "askSize": INTEGER,
      "bidSize": INTEGER,
      "volume": INTEGER,
      "count": INTEGER,
      "close": FLOAT,
      "open": FLOAT,
      "high": FLOAT,
      "low": FLOAT,
      "bid": FLOAT,
      "ask": FLOAT
    }
  ],
  "expiry": INTEGER,
  "symbol": STRING
}
```

- `date`: The date of the current data.
- `askCondition`: Condition of the ask.
- `bidCondition`: Condition of the bid.
- `openInterest`: Open interest.
- `bidExchange`: Exchange for the bid.
- `askExchange`: Exchange for the ask.
- `askSize`: Size of the ask.
- `bidSize`: Size of the bid.
- `volume`: Trading volume.
- `count`: Number of data points.
- `close`: Closing price.
- `open`: Opening price.
- `high`: Highest price.
- `low`: Lowest price.
- `bid`: The bidding price.
- `ask`: The asking price.

## Example Python Code to Load and Parse the Database

```python
import rocksdb
import json

def load_rocksdb_data(db_path, key):
    db = rocksdb.DB(db_path, rocksdb.Options(read_only=True))
    data = db.get(key.encode())
    if data is not None:
        decoded_data = data.decode('utf-8')
        parsed_data = json.loads(decoded_data)
        return parsed_data
    else:
        print("No data found for the specified key.")

# Example usage
db_path = "/path/to/rocksdb/database"
key = "SPY;d-stock-historical-eod;20230601;"
data = load_rocksdb_data(db_path, key)

if data is not None:
    print("Key:", key)
    print("Parsed Data:")
    print(json.dumps(data, indent=2))  # Print the parsed data with indentation
else:
    print("No data found for the specified key.")
```

This format should accurately represent the structure of your RocksDB key-value pairs and provide a clear example of how to load and parse the data using Python. Let me know if you need further assistance!