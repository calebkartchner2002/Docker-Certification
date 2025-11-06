# Section 22: Automated CI Workflows
- There are trends in the industry away from more formal CI/CD libraries like Jenkins and is leaning to more agile solutions like github actions
    - Provides a solution right next to your code that can do vulnerability scanning, unit tests, linting, building images, automated testing of images

## The Pull Request
- traditional work flow on project repository: Code changes are put into merge request, once approved this will start off the automation pipelines for deployment into production and other enviorments

## Automation is the Glue of DevOps
- Plugin system are becoming the primary feature of CI/CD pipelines, primarily in opensource using github
- People are increasingly developing common frameworks that can be easily integrated into your docker devops

## Basic PR Workflow
1. Push PR to github
1. Image will be built and immedietly be tested
1. This work flow can happen many times over; Once finalized the PR is merged into main branch
1. This then takes the built images and adds it to your container registry
1. Deploys a new container to your enviorments

- A few Linting companies have begun to shape up and gain more attention, almost everyone is moving these to be invovled in the CI/CD process:
    - `super-linter`
    - `megalinter`

## Intermediate PR Workflow
1. PR push 
1. Linting and image building done concurrently
1. Unit tests done on image
1. if these pass, integration test is used on entire product
1. if these pass, CVE scan is performed
1. Then can merge -> image build to prod -> image pushed to registry -> deployment to enviorments

## Advanced PR Workflow
1. Push PR
1. Image build, linting everything, SAST (security testing) all concurently
1. unit tests, CVE Scan, Integration test, deployment smoke test done concurrently next
1. Final production merge making final image, push to registry, deploying to enviorments
