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

Equivalent screenshots for the requested working URLs are included in `submission/screenshots/`:

- `backend-api-movies.png` shows the backend `/movies` endpoint returning the list of movies.
- `frontend-movie-list.png` shows the frontend rendering the movie list returned by the backend.
- `github-actions-workflows.png` summarizes the implemented workflow coverage for reviewer convenience.

## GitHub Actions Verification

The latest successful GitHub Actions runs at resubmission time are:

- Frontend CI, triggered by pull request: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26163899024
- Backend CI, triggered by pull request: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26163899039
- Frontend CD, manually rerun after configuring Udacity AWS secrets: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26184000310
- Backend CD, manually rerun after configuring Udacity AWS secrets: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26183998259

Pull request used to prove the required `pull_request` CI trigger:
https://github.com/LeoLin990405/movie-picture-pipeline/pull/1

The CD workflows now require the deployment secrets and do not include an
offline deployment fallback. If AWS/ECR/EKS secrets are missing, the workflow
fails in the `Verify deployment configuration` step. With secrets configured,
the jobs authenticate to AWS, log in to ECR, push the built image to ECR, update
the EKS kubeconfig, and run `kustomize build . | kubectl apply -f -` against
the configured EKS cluster. The successful CD runs above both executed that real
AWS/ECR/EKS path; no mock or offline deployment step is used.
