# Base image: ubuntu:22.04
FROM ubuntu:22.04

# ARGs
ARG TARGETPLATFORM=linux/amd64,linux/arm64
ARG DEBIAN_FRONTEND=noninteractive

# Install OpenJDK 17 for Neo4j compatibility
RUN apt-get update && \
    apt-get install -y openjdk-17-jdk wget gnupg software-properties-common && \
    apt-get clean

# Set JAVA_HOME for OpenJDK 17
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH="$JAVA_HOME/bin:${PATH}"

# neo4j 5.5.0 installation and some cleanup
RUN apt-get update && \
    apt-get install -y wget gnupg software-properties-common && \
    wget -O - https://debian.neo4j.com/neotechnology.gpg.key | apt-key add - && \
    echo 'deb https://debian.neo4j.com stable latest' > /etc/apt/sources.list.d/neo4j.list && \
    add-apt-repository universe && \
    apt-get update && \
    apt-get install -y nano unzip neo4j=1:5.5.0 python3-pip && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# TODO: Complete the Dockerfile

# Install git for repository cloning
RUN apt-get update && \
    apt-get install -y git && \
    apt-get clean

# Install Python dependencies
RUN pip3 install --upgrade pip && \
    pip3 install neo4j pandas pyarrow

# Set up environment variables for Neo4j
# ENV NEO4J_AUTH=neo4j/project1phase1

RUN neo4j-admin dbms set-initial-password project1phase1

# Download the NYC Trip data (March 2022)
WORKDIR /cse511
RUN wget https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2022-03.parquet

# Clone the private GitHub repository using GitHub Token
RUN git clone https://[GITHUB_TOKEN]/Fall-24-CSE511-Data-Processing-at-Scale/Project-1-amudra.git /cse511/Project-1-amudra

# Copy data_loader.py to /cse511 so the chmod command can access it
RUN cp /cse511/Project-1-amudra/cse511/data_loader.py /cse511/data_loader.py

# Add JDK version explicitly to neo4j.conf
# RUN echo "dbms.java.jvm_version=17" >> /etc/neo4j/neo4j.conf
# Add configurations to neo4j.conf
RUN echo "server.bolt.listen_address=0.0.0.0:7687" >> /etc/neo4j/neo4j.conf && \
    echo "server.http.listen_address=0.0.0.0:7474" >> /etc/neo4j/neo4j.conf && \
    echo "server.default_listen_address=0.0.0.0" >> /etc/neo4j/neo4j.conf && \
    echo "server.default_advertised_address=localhost" >> /etc/neo4j/neo4j.conf

#Download and install the GDS plugin (version 2.3.1)
RUN wget -O /var/lib/neo4j/plugins/neo4j-graph-data-science-2.3.1.jar \
    https://github.com/neo4j/graph-data-science/releases/download/2.3.1/neo4j-graph-data-science-2.3.1.jar


# Update neo4j.conf to allow GDS plugin
RUN echo "dbms.security.procedures.unrestricted=gds.*" >> /etc/neo4j/neo4j.conf && \
    echo "dbms.security.procedures.allowlist=gds.*" >> /etc/neo4j/neo4j.conf

# Run the data loader script
RUN chmod +x /cse511/data_loader.py && \
    neo4j start && \
    python3 data_loader.py && \
    neo4j stop

# Expose neo4j ports
EXPOSE 7474 7687

# Start neo4j service and show the logs on container run
CMD ["/bin/bash", "-c", "neo4j start && tail -f /dev/null"]
