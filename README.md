# Keptn Generic Job Service
![GitHub release (latest by date)](https://img.shields.io/github/v/release/keptn-sandbox/keptn-generic-job-service)
[![Go Report Card](https://goreportcard.com/badge/github.com/keptn-sandbox/keptn-generic-job-service)](https://goreportcard.com/report/github.com/keptn-sandbox/keptn-generic-job-service)

(naming not final)

This Keptn service introduces a radical new approach to running tasks with keptn. It provides the meand
to run any container as a Kubernetes Job orchestrated by keptn.

## Why?

The current approach of services running in the keptn ecosystem has the following downsides:

| Problem | Solution |
|----------------|----------------|
| Keptn services are constantly running while listening for cloud events coming in over NATS. This consumes unnecessary resources on your Kubernetes cluster. | By running the defined keptn tasks as short-lived workloads (Kubernetes Jobs), which just consume resources while the task is executed. |
| Whenever some new functionality should be triggered by keptn, a new keptn service needs to be written. It usually wraps the functionality of the wanted framework and executes it under the hood. The downside: The **code of the new keptn service needs to be written and maintained**. This effort scales linearly with the amount of services. | This service can execute any framework with just a few lines of yaml configuration. No need to write or maintain any new code.  |
| Keptn services usually filter for a static list of events the trigger the included functionality. This is not configurable. Whenever the service should listen to a new event, the code of the service needs to be changed. | The Generic Job Service provides the means to trigger a task execution for any keptn event. This is done by matching a jsonpath to the received event payload.  |
| File handling | |
| New functionality in keptn | |

## How?

### Event Matching

### Kubernetes Job

### File Handling


## Endless Possibilities

* Run the helm service as a kubernetes job and limit the permissions of it by assigning a different service account to it.
* Execute infrastructure as code with keptn (e.g. run terraform, pulumi, etc)
* Run kaniko inside the cluster and build and push your images. Suddenly, keptn turns into your CI

## Compatibility Matrix

*Please fill in your versions accordingly*

| Keptn Version    | [Keptn-Generic-Job-Service Docker Image](https://hub.docker.com/r/didiladi/keptn-generic-job-service/tags) |
|:----------------:|:----------------------------------------:|
|       0.8.2      | didiladi/keptn-generic-job-service:latest |

## Installation

The *keptn-generic-job-service* can be installed as a part of [Keptn's uniform](https://keptn.sh).

### Deploy in your Kubernetes cluster

To deploy the current version of the *keptn-generic-job-service* in your Keptn Kubernetes cluster, apply the [`deploy/service.yaml`](deploy/service.yaml) file:

```console
kubectl apply -f deploy/service.yaml
```

This should install the `keptn-generic-job-service` together with a Keptn `distributor` into the `keptn` namespace, which you can verify using

```console
kubectl -n keptn get deployment keptn-generic-job-service -o wide
kubectl -n keptn get pods -l run=keptn-generic-job-service
```

### Up- or Downgrading

Adapt and use the following command in case you want to up- or downgrade your installed version (specified by the `$VERSION` placeholder):

```console
kubectl -n keptn set image deployment/keptn-generic-job-service keptn-generic-job-service=didiladi/keptn-generic-job-service:$VERSION --record
```

### Uninstall

To delete a deployed *keptn-generic-job-service*, use the file `deploy/*.yaml` files from this repository and delete the Kubernetes resources:

```console
kubectl delete -f deploy/service.yaml
```

## Development

Development can be conducted using any GoLang compatible IDE/editor (e.g., Jetbrains GoLand, VSCode with Go plugins).

It is recommended to make use of branches as follows:

* `master` contains the latest potentially unstable version
* `release-*` contains a stable version of the service (e.g., `release-0.1.0` contains version 0.1.0)
* create a new branch for any changes that you are working on, e.g., `feature/my-cool-stuff` or `bug/overflow`
* once ready, create a pull request from that branch back to the `master` branch

When writing code, it is recommended to follow the coding style suggested by the [Golang community](https://github.com/golang/go/wiki/CodeReviewComments).

### Common tasks

* Build the binary: `go build -ldflags '-linkmode=external' -v -o keptn-generic-job-service`
* Run tests: `go test -race -v ./...`
* Build the docker image: `docker build . -t didiladi/keptn-generic-job-service:dev` (Note: Ensure that you use the correct DockerHub account/organization)
* Run the docker image locally: `docker run --rm -it -p 8080:8080 didiladi/keptn-generic-job-service:dev`
* Push the docker image to DockerHub: `docker push didiladi/keptn-generic-job-service:dev` (Note: Ensure that you use the correct DockerHub account/organization)
* Deploy the service using `kubectl`: `kubectl apply -f deploy/`
* Delete/undeploy the service using `kubectl`: `kubectl delete -f deploy/`
* Watch the deployment using `kubectl`: `kubectl -n keptn get deployment keptn-generic-job-service -o wide`
* Get logs using `kubectl`: `kubectl -n keptn logs deployment/keptn-generic-job-service -f`
* Watch the deployed pods using `kubectl`: `kubectl -n keptn get pods -l run=keptn-generic-job-service`
* Deploy the service using [Skaffold](https://skaffold.dev/): `skaffold run --default-repo=your-docker-registry --tail` (Note: Replace `your-docker-registry` with your DockerHub username; also make sure to adapt the image name in [skaffold.yaml](skaffold.yaml))


### Testing Cloud Events

We have dummy cloud-events in the form of [RFC 2616](https://ietf.org/rfc/rfc2616.txt) requests in the [test-events/](test-events/) directory. These can be easily executed using third party plugins such as the [Huachao Mao REST Client in VS Code](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

## Automation

### GitHub Actions: Automated Pull Request Review

This repo uses [reviewdog](https://github.com/reviewdog/reviewdog) for automated reviews of Pull Requests. 

You can find the details in [.github/workflows/reviewdog.yml](.github/workflows/reviewdog.yml).

### GitHub Actions: Unit Tests

This repo has automated unit tests for pull requests. 

You can find the details in [.github/workflows/tests.yml](.github/workflows/tests.yml).

### GH Actions/Workflow: Build Docker Images

This repo uses GH Actions and Workflows to test the code and automatically build docker images.

Docker Images are automatically pushed based on the configuration done in [.ci_env](.ci_env) and the two [GitHub Secrets](https://github.com/keptn-sandbox/keptn-generic-job-service/settings/secrets/actions)
* `REGISTRY_USER` - your DockerHub username
* `REGISTRY_PASSWORD` - a DockerHub [access token](https://hub.docker.com/settings/security) (alternatively, your DockerHub password)

## How to release a new version of this service

It is assumed that the current development takes place in the master branch (either via Pull Requests or directly).

To make use of the built-in automation using GH Actions for releasing a new version of this service, you should

* branch away from master to a branch called `release-x.y.z` (where `x.y.z` is your version),
* write release notes in the [releasenotes/](releasenotes/) folder,
* check the output of GH Actions builds for the release branch, 
* verify that your image was built and pushed to DockerHub with the right tags,
* update the image tags in [deploy/service.yaml], and
* test your service against a working Keptn installation.

If any problems occur, fix them in the release branch and test them again.

Once you have confirmed that everything works and your version is ready to go, you should

* create a new release on the release branch using the [GitHub releases page](https://github.com/keptn-sandbox/keptn-generic-job-service/releases), and
* merge any changes from the release branch back to the master branch.

## License

Please find more information in the [LICENSE](LICENSE) file.
