# EKS Service URL Evidence

This resubmission adds direct EKS service URL verification to both continuous deployment workflows. The reviewer feedback requested URLs or equivalent evidence from the EKS deployment rather than static screenshots, so the CD jobs now print the LoadBalancer service records and the resolved service URLs after `kubectl apply`.

## Successful CD Runs

- Backend CD run: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26203944204
- Frontend CD run: https://github.com/LeoLin990405/movie-picture-pipeline/actions/runs/26204010471

## Live Service URLs

- Backend API: http://a6410d12d710d4b95bb16cad2f0f0a30-1351506007.us-east-1.elb.amazonaws.com/movies
- Frontend app: http://a5182956fcc0d445599f57219e2e4688-1548340538.us-east-1.elb.amazonaws.com

## Runtime Verification

The backend service returned the expected movie payload from the live EKS LoadBalancer:

```json
{"movies":[{"id":"123","title":"Top Gun: Maverick"},{"id":"456","title":"Sonic the Hedgehog"},{"id":"789","title":"A Quiet Place"}]}
```

The frontend service returned the built React application HTML from the live EKS LoadBalancer.

GitHub Actions masks configured secret values in logs, so the raw workflow log replaces `us-east-1`, `backend`, and `frontend` with `***`. The service hostnames in this note use the same LoadBalancer hostnames printed by the successful runs with the deployment region restored.

Full downloaded workflow logs are included in:

- `submission/evidence/backend-cd-run-26203944204.log`
- `submission/evidence/frontend-cd-run-26204010471.log`
