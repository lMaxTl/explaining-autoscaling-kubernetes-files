## Dear reviewers,
following is a list of repositories developed during the reasearch on the project "Explaining and Visualizing Autoscaling Behavior of Microservice Systems Deployed on Kubernetes"
The repositories are organized in the following way:
- [explaining-autoscaling-frontend](https://github.com/lMaxTl/explaining-autoscaling-frontend)
- [explaining-autoscaling-backend](https://github.com/lMaxTl/explaining-autoscaling-backend)
- [explaining-autoscaling-backend-documentation](https://lmaxtl.github.io/documentation/ba-backend/overview.html)

EXPA is a tool that addresses the problem of lack of clear explanations for autoscaling behaviour in self-adaptive microservice systems. Traditional manual monitoring of such systems is time consuming, costly and can lead to long runtime interruptions. To meet the demand for automation, self-adaptive systems have become the industry standard. However, this also results in a lack of clear explanations for adaptation decisions, making it difficult to debug and verify the behaviour of these systems. EXPA aims to solve this problem by providing explanations for adaptation decisions in Kubernetes clusters, reducing the cost of ownership and increasing resilience.

## explaining-autoscaling-frontend
This repository contains the frontend of the project. It is a React application that is used to visualize the autoscaling behavior of the microservice system. The frontend is connected to the backend via a rest api connection. The frontend is deployed as a docker container on a Kubernetes cluster.

The interesting code files are located in the following folders:
 - components/ - contains the ui elements of the frontend
    - components/Cards/TabbableCard - contains the card that is used to display the metrics
    - components/DependencyGraph - contains the graph that is used to display the dependency graph
    - components/Events - contains the event list
    - components/Metrics/Display - contains the saved metric list
    - components/Metrics/Information - contains components that are used to display the information of a metric
    - components/Navbars - contains the navigation bar
    - components/Pods - contains the table that is used to display information about container in a pod
    - components/ReplicaSets - contains the component that is used to display information about a replica set 
    - components/Sidebars - contains the sidebar
    - components/Timelines - contains the timeline with and without metric information
    - components/Titles - contains a dynamic title component
 - helpers/
    - helpers/DataCollection - contains the code that is used to collect the data from the backend
    - helpers/DataManipulation - contains the code that is used to manipulate the data
    - helper/Types - contains the typescript types
 - pages/ - contains the pages of the frontend

## explaining-autoscaling-backend
This repository contains the backend of the project. It is a node.js application that is used to collect the metrics of the microservice system. The backend is deployed as a docker container on a Kubernetes cluster.
The project extends on the work of Tobias Rodestock. His work includes the following folders and are not part of the code review:
 - event-set/*
 - derivative/*
 - events.service.ts (partly, only consider receiveNewAdaptionEvent() and getAllMetricValues())



## Setup
To setup the project, you need to have a Kubernetes cluster running. The project was tested on a local Kubernetes cluster running on minikube. The project was also tested on a Kubernetes cluster running on Azure Cloud Platform.

It is recommended to use the following tools to setup the project:
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [helm](https://helm.sh/docs/intro/install/)
- [docker](https://docs.docker.com/get-docker/)
- [node.js](https://nodejs.org/en/download/)
- [npm](https://www.npmjs.com/get-npm)

Start the minikube cluster with the following command:
```bash
minikube start --memory 8192 --cpus 6 --extra-config=kubelet.housekeeping-interval=10s
```
It is important to check that the housekeeping-interval is set to 10s. Otherwise, the metrics-server will not work properly.

### Setup dependencies
This project is closely linked to the t2-project. Therefore, the t2-project needs to be setup first. The t2-project can be found [here](https://github.com/t2-project)

```bash	
# Add Databases
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install mongo --set auth.enabled=false bitnami/mongodb
helm install kafka bitnami/kafka
# Deploy T2Store
git clone https://github.com/t2-project/kube.git
cd kube/k8
kubectl create -f .
# add repo for prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# install charts
cd ..
cd prometheusfiles
helm install prometheus prometheus-community/kube-prometheus-stack -f ./prometheus-operator-values.yaml
helm install blackbox-exporter prometheus-community/prometheus-blackbox-exporter -f ./prometheus-blackbox-exporter-values.yaml
```

### Setup the Custom Metric Server
The custom metric server is required so that the Horizontal Pod Autoscaler can retrieve metrics from Prometheus. The custom metric server is deployed as a docker container on the Kubernetes cluster.

```bash	
cd .\custom_metric_server\
kubectl create -f .
cd ..
```

### Setup the Kubernetes Event Exporter
The Kubernetes Event Exporter is required so that the backend can retrieve events from the Kubernetes cluster. The Kubernetes Event Exporter is deployed as a docker container on the Kubernetes cluster.

```bash
cd .\kubernetes_event_exporter\
kubectl create -f .
cd ..
```

### Setup the HPA Config for t2store
These are example configurations derived by Tobias Rodestock and are used to scale the t2store microservice system.

```bash	
cd .\hpa_config\
kubectl create -f .
cd ..
```

### Setup the EXPA backend
Sometimes the backend errors and cant reach the promehteus service and is fixed by running minikube dashboard for some reason

```bash	
cd .\ba_backend\
kubectl create -f .
cd ..
```

### Setup the EXPA frontend
```bash	
cd .\ba_frontend\
kubectl create -f .
```

