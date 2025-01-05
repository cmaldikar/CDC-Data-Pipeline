# MySQL CDC Streaming to Kafka with Real-Time CSV Output

This project implements a **real-time change data capture (CDC)** pipeline where:
- Data is fetched from a **MySQL database**.
- Changes in the MySQL database are streamed using **Kafka**.
- The streamed data is then consumed and written to a **CSV file** in real-time.

## Project Structure

```
.
├── producer.py                # Producer script that streams data from MySQL to Kafka
├── consumer.py                # Consumer script that writes Kafka messages to CSV
├── mysql_data.csv             # CSV file where real-time data is stored
├── requirements.txt           # Python dependencies
└── README.md                  # Project documentation
```

## Prerequisites

### Dependencies
- Python 3.x
- Kafka (Local or Cluster)
- MySQL Database
- Required Python Libraries:
  - `confluent_kafka`
  - `mysql-connector-python`
  - `json`
  - `csv`

Install the necessary Python libraries by running the following command:

```bash
pip install -r requirements.txt
```

### Kafka Setup
- Install Kafka locally or use a managed Kafka service.
- Make sure the Kafka broker is running on the provided address (default: `localhost:9092`).

## Configuration

### Producer Configuration
In the `producer.py` script, configure the following settings:
- **KAFKA_TOPIC**: Kafka topic to send data to (default: `mysql_data`).
- **BROKER**: Kafka broker address (default: `localhost:9092`).
- **MYSQL_HOST**: Host address of the MySQL database.
- **MYSQL_USER**: MySQL username.
- **MYSQL_PASSWORD**: MySQL password.
- **MYSQL_DATABASE**: MySQL database name.
- **MYSQL_TABLE**: MySQL table from which data will be streamed.

### Consumer Configuration
In the `consumer.py` script, configure the following settings:
- **KAFKA_TOPIC**: Kafka topic to consume data from (default: `mysql_data`).
- **BROKER**: Kafka broker address (default: `localhost:9092`).
- **CSV_FILE**: Path to the CSV file where consumed data will be written (default: `mysql_data.csv`).

## How It Works

### Step 1: CDC from MySQL to Kafka (Producer)
The producer script fetches data from MySQL and streams the changes (new or updated rows) to Kafka. The `timestamp` column is used to track and filter new changes in the database. The script performs the following tasks:
- Fetches data from MySQL (only new rows or updated data).
- Streams the data to a Kafka topic (`mysql_data`).
- Sends the data in JSON format, converting `Decimal` and `datetime` types for proper serialization.

### Step 2: Consuming Kafka Data and Writing to CSV (Consumer)
The consumer script listens to the Kafka topic (`mysql_data`) and writes the received messages to a CSV file in real-time. The consumer performs the following tasks:
- Consumes messages from the Kafka topic.
- Writes the data to a CSV file, appending new records.
- Handles missing values by using default placeholders (`N/A`).

## How to Run

### Step 1: Start Kafka Broker
Ensure that Kafka is up and running. If you're running Kafka locally, use the appropriate command to start it (e.g., `bin/kafka-server-start.sh`).

### Step 2: Run the Producer Script
To start streaming data from MySQL to Kafka, run the producer script:

```bash
python producer.py
```

This will continuously fetch data from MySQL every 30 seconds (or as defined) and stream it to Kafka in real-time.

### Step 3: Run the Consumer Script
To start consuming data from Kafka and writing it to the CSV file, run the consumer script:

```bash
python consumer.py
```

The consumer will listen to the Kafka topic (`mysql_data`) and append the consumed messages to the `mysql_data.csv` file in real-time.

## Example Output

### Producer Output (Console)
```
Sent: {'id': 1, 'name': 'John Doe', 'amount': 100.50, 'timestamp': '2025-01-05T10:00:00'}
Sent: {'id': 2, 'name': 'Jane Smith', 'amount': 250.75, 'timestamp': '2025-01-05T10:05:00'}
...
```

### Consumer Output (Console)
```
Listening for data on topic mysql_data...
Received: {'id': 1, 'name': 'John Doe', 'amount': 100.50, 'timestamp': '2025-01-05T10:00:00'}
Received: {'id': 2, 'name': 'Jane Smith', 'amount': 250.75, 'timestamp': '2025-01-05T10:05:00'}
...
```

The CSV file (`mysql_data.csv`) will be updated with each new record:

```
id,name,amount,timestamp
1,John Doe,100.50,2025-01-05T10:00:00
2,Jane Smith,250.75,2025-01-05T10:05:00
...
```

### Real-Time Updates:
The CSV file will be updated in real-time as new data is streamed from MySQL through Kafka.

## Notes

- **Change Data Capture**: The producer fetches new or updated records from MySQL using a timestamp-based mechanism to ensure that only new data is processed.
- **Real-Time Processing**: The consumer appends new data to the CSV file as soon as it is received from Kafka, enabling real-time updates.
- **Kafka Offset Handling**: The consumer script is designed to consume data from the earliest available offset, ensuring that it processes all available messages.

## Troubleshooting

- **Kafka errors**: Verify that Kafka is running and the broker address is correctly configured.
- **MySQL connection errors**: Ensure that the MySQL credentials and host are correct.
- **CSV file issues**: Make sure you have permission to write to the specified location of the CSV file.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
