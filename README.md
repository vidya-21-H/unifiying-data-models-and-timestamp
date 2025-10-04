# unifiying-data-models-and-timestamp
import json
from datetime import datetime

def load_data(filename):
    """Load JSON data from a file."""
    try:
        with open(filename, 'r') as f:
            return json.load(f)
    except FileNotFoundError:
        print(f"File {filename} not found.")
        return []
    except json.JSONDecodeError:
        print(f"Error decoding JSON in {filename}.")
        return []

def process_model1(record):
    """Convert model1 record to unified format."""
    try:
        timestamp = datetime.fromisoformat(record.get("time", "1970-01-01T00:00:00"))
        value = record.get("value", 0)
        return {"timestamp": timestamp, "value": value}
    except Exception as e:
        print(f"Error processing record {record}: {e}")
        return None

def process_model2(record):
    """Convert model2 record to unified format."""
    try:
        timestamp = datetime.strptime(record.get("date", "1970-01-01 00:00:00"), "%Y-%m-%d %H:%M:%S")
        value = record.get("amount", 0)
        return {"timestamp": timestamp, "value": value}
    except Exception as e:
        print(f"Error processing record {record}: {e}")
        return None

def implement_me():
    data1 = load_data('data-1.json')
    data2 = load_data('data-2.json')

    # Process and filter out any None results
    unified_data1 = [rec for rec in (process_model1(r) for r in data1) if rec]
    unified_data2 = [rec for rec in (process_model2(r) for r in data2) if rec]

    combined_data = unified_data1 + unified_data2
    combined_data.sort(key=lambda x: x['timestamp'])

    # Convert datetime to ISO string
    for rec in combined_data:
        rec['timestamp'] = rec['timestamp'].isoformat()

    # Write output to a file
    with open('combined.json', 'w') as f:
        json.dump(combined_data, f, indent=2)

    return combined_data

# Run the function and print first 5 records for verification
if __name__ == "__main__":
    result = implement_me()
    print(result[:5])  # display first 5 combined records
