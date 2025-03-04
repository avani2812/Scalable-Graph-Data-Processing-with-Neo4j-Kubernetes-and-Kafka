# Scalable-Graph-Data-Processing-with-Neo4j-Kubernetes-and-Kafka


## Abstract
This project explores graph-based data analysis using Neo4j, focusing on key graph algorithms such as PageRank and Breadth-First Search (BFS). It is implemented in two phases:

- **Phase 1:** Setting up a Dockerized Neo4j environment and implementing core graph algorithms using the NYC Yellow Cab Trip dataset.
- **Phase 2:** Scaling the system using Kubernetes and Kafka to develop a distributed, real-time data processing pipeline.

This transition from a single-instance setup to a distributed architecture demonstrates improvements in scalability, availability, and performance in graph data analysis.

---

## Table of Contents
- [Introduction](#introduction)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Results](#results)
- [Contributors](#contributors)

---

## Introduction
Graph data analysis is crucial in fields like social networks, bioinformatics, and transportation. This project models NYC Yellow Cab Trip data as a graph, where pickup and drop-off locations are nodes, and trips are relationships. It applies graph algorithms to extract insights, transitioning from a single-instance Neo4j setup to a distributed system using Kubernetes and Kafka.

---

## Technologies Used
- **Neo4j 5.5.0** - Graph database
- **Docker** - Containerized environment
- **Kubernetes (Minikube)** - Orchestration for distributed deployment
- **Apache Kafka** - Real-time data streaming
- **Python** - Data transformation and processing
- **Graph Data Science (GDS) Library** - Advanced graph analytics

---

## Project Structure
```bash
├── phase1/
│   ├── Dockerfile
│   ├── data_loader.py
│   ├── interface.py
│   ├── tester.py
│   ├── yellow_tripdata_2022-03.parquet

├── phase2/
│   ├── interface.py
│   ├── kafka-neo4j-connector.yaml
│   ├── zookeeper-setup.yaml
│   ├── kafka_setup.yaml
│   ├── neo4j-values

```

---

## Installation
### Phase 1: Local Neo4j Setup
1. Clone the repository:
   ```sh
   git clone https://github.com/your-repo/graph-processing
   cd graph-processing/Phase1_Graph_Data_Processing
   ```
2. Build and run the Docker container:
   ```sh
   docker build -t neo4j_graph .
   docker run -p 7474:7474 -p 7687:7687 neo4j_graph
   ```
3. Load the dataset using the provided Python script:
   ```sh
   python data_loader.py
   ```
4. Run graph algorithms:
   ```sh
   docker exec -it graph-container bash -c "python3 /workspace/tester.py"
   ```

### Phase 2: Distributed Deployment
1. Navigate to the phase2 directory:
   ```sh
   cd ../Phase2_Scalable_Data_Processing
   ```
2. Start Kubernetes and deploy Kafka:
   ```sh
   minikube start
   kubectl apply -f zookeeper-setup.yaml
   kubectl apply -f kafka_setup.yaml
   kubectl apply -f neo4j-values.yaml
   kubectl apply -f kafka-neo4j-connector.yaml
   ```
3. Start real-time data ingestion:
   ```sh
   python data_producer.py
   ```
4. Run graph algorithms on real-time data:
   ```sh
   python interface.py
   docker exec -it graph-container bash -c "python3 /workspace/tester.py"
   ```
5. Verify Graph Data in Neo4j
   ```sh
   MATCH (n) RETURN n LIMIT 20;
   MATCH ()-[r:TRIP]->() RETURN COUNT(r);
   ```

---

## Usage
### Running PageRank Algorithm (Neo4j Cypher Query)
```cypher
CALL gds.pageRank.stream('page_rank_graph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).LocationID AS location, score
ORDER BY score DESC;
```

### Running BFS Algorithm (Neo4j Cypher Query)
```cypher
CALL gds.bfs.stream('bfs_graph', {startNode: 'Pickup_Location', endNode: 'Dropoff_Location'})
YIELD path
RETURN path;
```

---

## Results
- **Phase 1:** Successfully implemented PageRank and BFS algorithms in a Neo4j Dockerized setup.
- **Phase 2:** Integrated Kafka for real-time data ingestion and deployed a distributed system using Kubernetes.
- Kafka processed 1530 trip relationships and created 42 location nodes.

---

## Contributors
- Avani Mundra (@avani2812)
