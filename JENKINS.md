# Jenkins CI/CD Assignment

Build a production-grade CI/CD pipeline.

---

## Prerequisites

- Jenkins setup complete (JENKINS-SETUP.md)
- K8s cluster running
- Docker Hub account
- Completed K8s assignment

---

## Goal

Automate: Code push → Build → Test → Deploy → Verify

---

## Part 1: Create Jenkinsfile (2 hours)

Create `Jenkinsfile` in project root with these stages:

### Stage 1: Checkout
Pull code from Git.

### Stage 2: Build
Install dependencies and build Docker image.

**Questions:**
- How to tag images with build number?
- Should you also tag as 'latest'?

### Stage 3: Push
Push image to Docker Hub.

**Research:** How to use Jenkins credentials?

### Stage 4: Deploy
Deploy to K8s cluster.

**Tasks:**
- Update deployment image
- Apply manifests
- Wait for rollout

**Research:** How to update only the image in K8s?

### Stage 5: Verify
Check if deployment succeeded.

**Tasks:**
- Check pod status
- Test health endpoint

---

## Part 2: Configure Jenkins Job (30 min)

**Jenkins → New Item → Pipeline**

Name: `campusfit-cicd`

**Configuration:**
- Definition: Pipeline script from SCM
- SCM: Git
- Repository URL: your-repo
- Branch: main
- Script Path: Jenkinsfile

**Build Triggers:**
- Poll SCM: `H/5 * * * *`

---

## Part 3: Test Pipeline (1 hour)

### Test 1: Clean Deploy
Trigger manually, verify deployment.

### Test 2: Code Change
Change APP_ENV, push, verify auto-trigger.

### Test 3: Failure Handling
Break something, watch pipeline fail, fix it.

---

## Part 4: Improvements (1 hour)

Add:
1. Environment variables
2. Error handling
3. Clear success/failure messages
4. Cleanup old Docker images

---

## Advanced (Optional - No Extra Setup Needed)

### Multi-Environment
Deploy to `dev` namespace automatically, `prod` with approval.

**Research:** Jenkins input step for manual approval.

### Parallel Stages
Run tests and linting in parallel.

### Post-Deployment Tests
Automatically test endpoints after deploy.

```groovy
sh 'curl -f http://service/health || exit 1'
```

### Rollback on Failure
If tests fail, rollback deployment automatically.

**All run on Jenkins master - no agent needed.**

---

## Real-World Questions

Document your thoughts:

1. Why is 'latest' tag bad in production?
2. How to handle secrets in pipeline?
3. How to ensure zero-downtime deployments?
4. How fast can you rollback if prod breaks?

---

## Submission

### Code:
- [ ] Jenkinsfile in repo
- [ ] Pushed to GitHub

### Screenshots:
- [ ] Successful pipeline run
- [ ] Failed build with error handling
- [ ] Deployed pods in K8s
- [ ] Jenkins build history

### Documentation (500 words):
- How each stage works
- Credential management
- Deployment verification
- Challenges and solutions
- What you'd improve for production

---

## Grading

| Item | Points |
|------|--------|
| Pipeline works end-to-end | 25 |
| Docker build and push | 20 |
| K8s deployment | 20 |
| Verification step | 10 |
| Error handling | 10 |
| Code quality | 10 |
| Documentation | 15 |
| Total | 110 |

**Bonus (+5 each):**
- Multi-environment
- Post-deployment tests
- Parallel stages
- Rollback automation

---

## Tips

1. Start simple, add features incrementally
2. Test each stage independently
3. Check Console Output for errors
4. `docker exec jenkins <command>` to debug

---

Time: 4-5 hours

This is real DevOps work. Learn by doing.
