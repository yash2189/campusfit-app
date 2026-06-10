FROM jenkins/jenkins:lts

USER root

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y docker.io && \
    rm -rf /var/lib/apt/lists/*

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && \
    mv kubectl /usr/local/bin/

# Add Jenkins user to docker group (GID will be set via docker-compose)
RUN usermod -aG docker jenkins

USER jenkins

# Pre-install plugins
RUN jenkins-plugin-cli --plugins \
    docker-workflow \
    kubernetes-cli \
    git \
    workflow-aggregator
