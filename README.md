# Fuse Integration Services (FIS) on OpenShift with Camel on Karaf JVM (blueprint) using Source to Image(S2I) build strategy.

This quickstart shows a simple Apache Camel application that logs a message to the server log every 5th second.

This example is implemented using solely the XML DSL (there is no Java code). The source code is provided in the following XML file `src/main/resources/OSGI-INF/blueprint/camel-log.xml`.
It also shows how Karaf assembly files can be overriden using resources from `src/main/resources/assembly/`. The included sample log file `etc/org.ops4j.pax.logging.cfg` sets the log level to DEBUG. 

### Building

The example can be built with

    mvn clean install

### Running the example in fabric8

It is assumed that OpenShift platform is already running. If not you can find details how to [Install OpenShift at your site](https://docs.openshift.com/enterprise/3.1/install_config/install/index.html).

Login into OpenShift:

    oc login -u <username> -p <password/token> -n <openshift namespace>  <https://CLUSTER_SERVER_API_HOST:8443>


    oc login -u configadmin -p password -n fis-openshift-fabric8-maven  https://192.168.56.101:8443

Hint:- To get OpenShift/Kubernetes cluster server API URL
    
    oc get ep -o "jsonpath=https://{$.items[?(@.metadata.name==\"kubernetes\")].subsets[0].addresses[0].ip}:{$.items[?(@.metadata.name==\"kubernetes\")].subsets[0].ports[?(@.name==\"https\")].port}"

Hint:- To get Docker Registry's ClusterIP and Port:

    oc get svc/docker-registry -n default -o 'jsonpath={.spec.clusterIP}:{.spec.ports[0].port}'
    
The example can be built and deployed using a single goal:

    mvn -Pf8-deploy \
	-Ddocker.pull.registry=<Docker registry host to pull an image> \
	-Ddocker.push.registry=<Docker registry host to pull an image> \
	-Ddocker.push.username=<OpenShift username> \
	-Ddocker.push.password=<OpenShift user passowrod/token> \
    -Dopenshift.project.name=<OpenShift project/namespace>


    mvn -Pf8-deploy \
	-Ddocker.pull.registry=registry.access.redhat.com \
	-Ddocker.push.registry=$(oc get svc/docker-registry -n default -o 'jsonpath={.spec.clusterIP}:{.spec.ports[0].port}') \
	-Ddocker.push.username=$(oc whoami) \
	-Ddocker.push.password=$(oc whoami -t) \
	-Dopenshift.project.name=$(oc project -q)

When the example runs in OpenShift, you can use the OpenShift client tool to inspect the status

To list all the running pods:

    oc get pods

Then find the name of the pod that runs this quickstart, and output the logs from the running pods with:

    oc logs <name of pod>

You can also use the OpenShift [web console](https://docs.openshift.com/enterprise/3.1/getting_started/developers/developers_console.html#tutorial-video) to manage the
running pods, and view logs and much more.


### Running the example using OpenShift S2I template

The example can also be built and run using the included S2I template quickstart-template.json.

The application can be run directly by first editing the template file and populating S2I build parameters, including the required parameter GIT_REPO and then executing the command:

    oc new-app -f quickstart-template.json

Alternatively the template file can be used to create an OpenShift application template by executing the command:

    oc create -f quickstart-template.json


### More details

You can find more details about running this [quickstart](http://fabric8.io/guide/quickstarts/running.html) on the website. This also includes instructions how to change the Docker image user and registry.

