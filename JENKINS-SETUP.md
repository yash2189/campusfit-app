# Jenkins Setup Guide

Simple Jenkins setup for the pipeline assignment.

---

## Quick Setup (One Command)

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

**Wait 2 minutes for Jenkins to start.**

---

## Initial Setup

### 1. Get Password
```bash
docker logs jenkins 2>&1 | grep -A 2 "Please use the following password"
```

Copy the password.

### 2. Open Jenkins
http://localhost:8080

Paste password → Install suggested plugins → Create admin user

---

## Install Required Plugins

**Manage Jenkins → Plugins → Available plugins**

Search and install:
- Docker Pipeline
- Kubernetes CLI Plugin

Click "Install" → Check "Restart Jenkins when installation is complete"

---

## Add Docker Hub Credentials

**Manage Jenkins → Credentials → System → Global credentials → Add Credentials**

- Kind: Username with password
- Username: your-dockerhub-username
- Password: your-dockerhub-password
- ID: `dockerhub-credentials`

Click "Create"

---

## Install Tools in Jenkins

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

# Restart Jenkins
docker restart jenkins
```

Wait 1 minute for restart.

---

## Verify Setup

```bash
# Check Docker
docker exec jenkins docker ps

# Check kubectl
docker exec jenkins kubectl get nodes
```

Both should work without errors.

---

## Troubleshooting

### Jenkins UI not loading
```bash
docker logs jenkins
# Wait 2 minutes if still starting
```

### Docker permission denied
```bash
docker restart jenkins
# Jenkins needs access to Docker socket
```

### kubectl can't connect

**For Minikube:**
```bash
# Ensure Minikube is running
minikube status
```

**For k3s:**
```bash
# Copy k3s config
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER ~/.kube/config

# Restart Jenkins to pick up changes
docker restart jenkins
```

---

## Done

If all verifications pass, proceed to JENKINS.md for the assignment.
