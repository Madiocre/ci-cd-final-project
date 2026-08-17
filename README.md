# CI-CD-final-project

This repository is the final project for the IBM CI/CD course. It demonstrates a
complete continuous integration and continuous delivery setup: automated linting
and testing on every push via GitHub Actions, and an automated build and deploy
pipeline running on OpenShift with Tekton.

## Tech stack

- **Runtime:** Node.js 20
- **Testing:** Jest
- **Linting:** ESLint
- **CI:** GitHub Actions
- **CD:** Tekton Pipelines on OpenShift

## Continuous Integration

CI runs on every push and pull request to `main`, defined in
[`.github/workflows/workflow.yml`](.github/workflows/workflow.yml).

The workflow checks out the code, sets up Node.js 20, installs dependencies with
`npm ci`, then runs two quality gates:

| Step | Command | Purpose |
|------|---------|---------|
| Lint with ESLint | `npm run lint` | Enforces code style and catches common errors |
| Run unit tests with Jest | `npm test` | Runs the unit test suite |

## Continuous Delivery

The Tekton pipeline is defined in [`.tekton/`](.tekton/), with reusable tasks in
[`.tekton/tasks.yml`](.tekton/tasks.yml).

| Task | What it does |
|------|--------------|
| `cleanup` | Clears the shared workspace before a new run begins |
| `git-clone` | Clones the repository into the workspace |
| `eslint` | Lints the source with ESLint |
| `jest-test` | Runs the Jest unit test suite |
| `buildah` | Builds the container image and pushes it to the registry |
| `deploy` | Deploys the image to OpenShift |

Tasks share a workspace backed by a PersistentVolumeClaim so that each step
operates on the same checked-out source.
