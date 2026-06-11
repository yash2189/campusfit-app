# Jenkins Setup Guide

Two setup methods - use whichever works for you.

---

## Prerequisites

- Docker installed
- K8s cluster running (Minikube or k3s)

---

## Method 1: Docker Compose (Recommended)

### 1. Start Jenkins

```bash
cd jenkins
docker-compose up -d
```

Wait 2 minutes.

### 2. Get Password

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### 3. Open Jenkins

http://localhost:8080

Paste password → Install suggested plugins → Create admin user

---

## Method 2: Docker Run (Fallback)

If Docker Compose doesn't work:

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/.kube:/root/.kube:ro \
  --group-add $(stat -c '%g' /var/run/docker.sock) \
  jenkins/jenkins:lts
```

Then install tools manually:

```bash
# Install Docker CLI
docker exec -u root jenkins bash -c "
apt-get update && apt-get install -y docker.io
"

# Install kubectl
docker exec -u root jenkins bash -c "
curl -LO https://dl.k8s.io/release/\$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl && \
chmod +x kubectl && \
mv kubectl /usr/local/bin/
"

# Install plugins
docker exec jenkins bash -c "
jenkins-plugin-cli --plugins docker-workflow kubernetes-cli
"

# Restart
docker restart jenkins
```

---

## Add Docker Hub Credentials

**Manage Jenkins → Credentials → System → Global credentials → Add**

- Kind: Username with password
- Username: your-dockerhub-username
- Password: your-dockerhub-password
- ID: `dockerhub-credentials`

---

## Verify

```bash
# Docker works
docker exec jenkins docker ps

# kubectl installed
docker exec jenkins kubectl version --client

# kubectl reaches cluster
docker exec jenkins kubectl get nodes
```

All should work.

---

## Troubleshooting

### kubectl can't connect (k3s only)

k3s uses 127.0.0.1 which doesn't work from containers.

```bash
# Get your IP
ip addr show | grep "inet " | grep -v 127.0.0.1

# Edit k3s config
sudo nano /etc/rancher/k3s/k3s.yaml
# Change server: https://127.0.0.1:6443
# To: server: https://YOUR_IP:6443

# Copy to standard location
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config

# Restart Jenkins
docker restart jenkins
```

### Minikube users

Should work automatically.

```bash
minikube status  # ensure running
```

### Docker permission denied

```bash
docker restart jenkins
```

---

## What's Included

✅ Docker CLI
✅ kubectl
✅ Git
✅ Required plugins

---

## Stop/Start

```bash
# Method 1
docker-compose down
docker-compose up -d

# Method 2
docker stop jenkins
docker start jenkins
```

---

Done! Proceed to JENKINS.md for the assignment.
