# Movie Picture Pipeline Submission Notes

## Implemented Workflows

- `frontend-ci.yaml` implements the `Frontend Continuous Integration` workflow for pull requests to `main` and manual runs. It runs frontend lint and tests in parallel, then builds the Docker image only after both checks pass.
- `backend-ci.yaml` implements the `Backend Continuous Integration` workflow for pull requests to `main` and manual runs. It runs backend lint and tests in parallel, then builds the Docker image only after both checks pass.
- `frontend-cd.yaml` implements the `Frontend Continuous Deployment` workflow for pushes to `main` and manual runs. It runs lint, tests, Docker build with `REACT_APP_MOVIE_API_URL`, ECR login, ECR push, and EKS deployment with `kustomize` and `kubectl`.
- `backend-cd.yaml` implements the `Backend Continuous Deployment` workflow for pushes to `main` and manual runs. It runs lint, tests, Docker build, ECR login, ECR push, and EKS deployment with `kustomize` and `kubectl`.

## Required Secrets

The CD workflows read AWS and deployment configuration from GitHub Secrets:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN` for temporary Udacity credentials
- `AWS_REGION`
- `EKS_CLUSTER_NAME`
- `BACKEND_ECR_REPOSITORY`
- `FRONTEND_ECR_REPOSITORY`
- `REACT_APP_MOVIE_API_URL`

No AWS credentials are committed to the repository.

## Working Application Evidence

Direct EKS service URL evidence is included in `submission/eks-service-urls.md`.
The latest successful CD workflows print the Kubernetes LoadBalancer records and
the resulting service URLs after deployment:

- Backend service URL: http://a6410d12d710d4b95bb16cad2f0f0a30-1351506007.us-east-1.elb.amazonaws.com/movies
- Frontend service URL: http://a5182956fcc0d445599f57219e2e4688-1548340538.us-east-1.elb.amazonaws.com

Equivalent screenshots for the working URLs are also included in `submission/screenshots/`:

- `backend-api-movies.png` shows the backend `/movies` endpoint returning the list of movies.
- `frontend-movie-list.png` shows the frontend rendering the movie list returned by the backend.
- `github-actions-workflows.png` summarizes the implemented workflow coverage for reviewer convenience.

## GitHub Actions Verification

The latest successful GitHub Actions runs at resubmission time are:

- Frontend CI, triggered by pull request: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26163899024
- Backend CI, triggered by pull request: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26163899039
- Frontend CD, manually rerun after configuring Udacity AWS secrets and service URL verification: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26204010471
- Backend CD, manually rerun after configuring Udacity AWS secrets and service URL verification: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26203944204

Pull request used to prove the required `pull_request` CI trigger:
https://github.com/LeoLin990405/movie-picture-pipeline/pull/1

The CD workflows now require the deployment secrets and do not include an
offline deployment fallback. If AWS/ECR/EKS secrets are missing, the workflow
fails in the `Verify deployment configuration` step. With secrets configured,
the jobs authenticate to AWS, log in to ECR, push the built image to ECR, update
the EKS kubeconfig, and run `kustomize build . | kubectl apply -f -` against
the configured EKS cluster. The successful CD runs above both executed that real
AWS/ECR/EKS path, waited for the deployment to become available, ran
`kubectl get service`, and printed the service URL from the assigned
LoadBalancer hostname. No mock or offline deployment step is used.
