# KafkaUI via Docker

This guide explains how to set up and use **Kafka UI** locally with Docker for debugging and inspecting Kafka topics.

---

## 1. List Available Topics

To list the topics in a running Kafka cluster:

    kafka-topics --bootstrap-server <KAFKA_BOOTSTRAP_SERVER> --list

Example:

    kafka-topics --bootstrap-server my-cluster.kafka.local:9094 --list

---

## 2. Get Kafka Cluster ID

To retrieve the cluster ID of your local or remote Kafka cluster:

    kafka-cluster cluster-id --bootstrap-server <KAFKA_BOOTSTRAP_SERVER>

Example:

    kafka-cluster cluster-id --bootstrap-server my-cluster.kafka.local:9094

You will need this **Cluster ID** in the next step.

---

## 3. Run Kafka UI with Docker

Run the Kafka UI Docker container, replacing `<CLUSTER_ID>` and `<KAFKA_BOOTSTRAP_SERVER>` with your values:

```
docker run -d -p 8080:8080
-e KAFKA_CLUSTERS_0_NAME=<CLUSTER_ID>
-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_BOOTSTRAP_SERVER>
provectuslabs/kafka-ui
```

Example:

```
docker run -d -p 8080:8080
-e KAFKA_CLUSTERS_0_NAME=abcd1234ClusterId
-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=my-cluster.kafka.local:9094
provectuslabs/kafka-ui
```

---

## 4. Access Kafka UI

Once the container is running, open your browser and navigate to: `http://localhost:8080`

You will see the Kafka UI interface where you can:

- Browse topics
- View messages
- Manage consumer groups
- Inspect configurations

---

## Notes

- **Docker** must be installed and running on your local machine.
- Ensure that **port 8080** is not already in use. You can check by running: `lsof -i:8080`
- Replace placeholder values (`<CLUSTER_ID>` and `<KAFKA_BOOTSTRAP_SERVER>`) with actual values from your environment.
