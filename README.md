[https://alanrb.medium.com/building-signing-and-verifying-docker-images-for-golang-apps-with-gitlab-ci-cd-kaniko-and-3f923e31f15d](https://alanrb.medium.com/building-signing-and-verifying-docker-images-for-golang-apps-with-gitlab-ci-cd-kaniko-and-3f923e31f15d "https://alanrb.medium.com/building-signing-and-verifying-docker-images-for-golang-apps-with-gitlab-ci-cd-kaniko-and-3f923e31f15d")

### **Explanation of the `.gitlab-ci.yml` File**

1. **Stages**:
   - `build`: Builds the Docker image using Kaniko.
   - `sign`: Signs the Docker image using Cosign.
   - `verify`: Verifies the signed Docker image using Cosign.
   - `deploy`: Deploys the image to staging and production environments.

2. **Variables**:
   - `IMAGE_NAME`: The name of your Docker image.
   - `ACR_NAME`: The Azure Container Registry (ACR) name.
   - `IMAGE_TAG`: The tag for the Docker image (default is `latest`).

3. **Build Stage**:
   - Uses Kaniko to build the Docker image and push it to the Azure Container Registry.
   - Two tags are pushed: `latest` and the Git commit short SHA (`${CI_COMMIT_SHORT_SHA}`).

4. **Sign Stage**:
   - Uses Cosign to sign the Docker image with the private key (`$COSIGN_PRIVATE_KEY`).
   - Signs both the `latest` and commit SHA tags.

5. **Verify Stage**:
   - Uses Cosign to verify the signed Docker image with the public key (`$COSIGN_PUBLIC_KEY`).
   - Verifies both the `latest` and commit SHA tags.

6. **Deploy Stage**:
   - **Staging Deployment**:
     - Deploys the image to the `staging` environment.
     - Uses the `$STAGING_API_KEY` environment variable (scoped to the `staging` environment).
     - Triggered only on the `main` branch.
   - **Production Deployment**:
     - Deploys the image to the `production` environment.
     - Uses the `$PRODUCTION_API_KEY` environment variable (scoped to the `production` environment).
     - Triggered only when a Git tag is created.

---

### **GitLab CI/CD Variables**
Make sure to add the following variables in your GitLab project under **Settings > CI/CD > Variables**:
- `COSIGN_PRIVATE_KEY`: The private key for signing images.
- `COSIGN_PUBLIC_KEY`: The public key for verifying images.
- `STAGING_API_KEY`: The API key for the staging environment (scoped to `staging`).
- `PRODUCTION_API_KEY`: The API key for the production environment (scoped to `production`).
