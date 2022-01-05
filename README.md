# hello-fun

This repo provides a simple Hello web app based on Spring Boot and Spring Cloud Function.

It can be deployed as a standalone web app, as a Tanzu Application Platform workload resource or, as a Kubernetes Deployment and Service.

## The code

> **NOTE**: The project is configured for Java 11, if you prefer a different version, then modify the `java.version` property in `pom.xml`.

The project contains the following Spring Cloud Function bean definition:

```text
	@Bean
	public Function<String, String> hello() {
		return (in) -> {
			return "Hello " + in;
		};
	}
```

This simple function returns the input value, prefixed with "Hello ". This is just a simple example what a Spring Cloud Function can do. 
It is defined in `src/main/java/com/example/helloapp/HelloAppApplication.java`

## Deployment

This app can be deployed as a stand-alone web app, as a Tanzu Application Platform (TAP) workload resource or, as a Kubernetes Deployment and Service.

### Standalone app with embedded Tomcat server

You can build the project using Maven:

```bash
mvn clean package
```

To run the app using the embedded Tomcat server you can run this command:

```bash
mvn spring-boot:run
```

You can access the function using `curl`:

```bash
curl -w'\n' -H 'Content-Type: text/plain' localhost:8080 -d "Fun"
```
### Deploying to Kubernetes as a TAP workload with Tilt

> NOTE: The provided `config/workload.yaml` file uses the Git URL for this sample. When you want to modify the source, you must push the code to your own Git repository and then update the `spec.source.git` information in the `config/workload.yaml` file.

You can containerize this template app and deploy it as a Tanzu Application Platform (TAP) workload.
You need to have TAP installed on your cluster.
See the [VMware Tanzu Application Platform documentation](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/index.html) for details.

We have included a `Tiltfile` file to make this easier when deploying to a cluster.

For best results use Tilt version v0.23.2 or later. You can install Tilt by following these instructions: https://docs.tilt.dev/install.html

To set up the deployment environment set the CURRENT_CONTEXT environment variable.

Set CURRENT_CONTEXT using:

```
export CURRENT_CONTEXT=$(kubectl config current-context)
```

To build and deploy the app run:

```
tilt up
```

and follow the instructions (hitting space bar brings up the Tilt interface in your browser).

To uninstall the app run:

```
tilt down
```

### Accessing the app deployed to your cluster

If you don't have `curl` installed it can be installed using downloads here: https://curl.se/download.html

Determine the URL to use for the accessing the app by running:

```
tanzu apps workload get hello-fun
```

To invoke the deployed function run the following `curl` command in another terminal window:

```
curl <URL> -w'\n' -H 'Content-Type: text/plain' -d Fun
```

This depends on the TAP installation having DNS configured for the Knative ingress.

### Deploying to Kubernetes as a TAP workload with Tanzu CLI

If you make modifications to the source and push to your own Git repository, then you can update the `spec.source.git` information in the `config/workload.yaml` file.

When you are done developing your function app, you can simply deploy it using:

```
tanzu apps workload apply -f config/workload.yaml
```

If you would like deploy the code from your local working directory you can use the following command:

```
tanzu apps workload create hello-fun -f config/workload.yaml \
  --local-path . \
  --source-image <REPOSITORY-PREFIX>/hello-fun-source \
  --type web
```
