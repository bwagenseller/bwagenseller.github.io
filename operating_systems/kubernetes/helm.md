# Helm  

# What is Helm?  


[Helm](https://helm.sh/) is a way to describe a [Kubernetes](operating_systems/kubernetes/) deployment. Helm manages Charts, and Charts are packages of pre-configured Kubernetes resources. It is written in [YAML](learn_to_code/yaml) using a [Go](https://go.dev/) template.  

# Installing Helm (Ubuntu)  

To install Helm:  
1\. Download your desired version from [Helm's Github page](https://github.com/helm/helm/releases).  

2\. Unpack: `tar -zxvf helm-v3.12.1-linux-amd64.tar.gz` 
* Obviously your file name may be different.  

3\. There are very few files in the zip file - find the one literally called `helm` and move it to the `/usr/local/bin` directory:  <br>`mv linux-amd64/helm /usr/local/bin/helm`
* You may want to do this as root.  

4\. Delete the downloaded file and temp directory that was created.  

# Creating New Helm Charts  

To create a new set of default Helm files, navigate to the directory where you want the base of your charts to be located and then type:  
```
helm create myNewHelmChart
```  
* `myNewHelmChart` is the name of the chart we are making.  
   * A directory with this name will be created.  
   * All Helm files will be created in this directory.  

# Helm Files and Directories  


## Base Directory (Maven)  

When used in a Java project that uses [Maven](learn_to_code/java/maven), Helm is typically located in a directory named `helm` located in the base directory (next to the [src directory](learn_to_code/java/maven?id=locations-of-files-in-maven)).  

## Chart.yaml  

The file <font color="green">Chart.yaml</font> is located in the `helm` directory and seems to be the 'master' file. 

An example:  
```
apiVersion: v2
name: mylibchart
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.16.0"
```  
* This is the default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts).  
* Comments are made with a `#`.  
* `apiVersion` is the version of _Helm_ itself.  
* `appVersion` is the version of the app being managed by this chart (i.e. most likely the app you wrote or are maintaining in Java, C, etc etc).  
* `version` is the version of the chart, and should be updated when the app is updated.  

## deployment.yaml  

The file <font color="green">deployment.yaml</font> is located in the `helm/templates` directory.  

An example file:  
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myNewHelmChart.fullname" . }}
  labels:
    {{- include "myNewHelmChart.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount }}
  {{- end }}
  selector:
    matchLabels:
      {{- include "myNewHelmChart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      labels:
        {{- include "myNewHelmChart.selectorLabels" . | nindent 8 }}
    spec:
      {{- with .Values.imagePullSecrets }}
      imagePullSecrets:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      serviceAccountName: {{ include "myNewHelmChart.serviceAccountName" . }}
      securityContext:
        {{- toYaml .Values.podSecurityContext | nindent 8 }}
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: {{ .Values.service.port }}
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      {{- with .Values.affinity }}
      affinity:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
```  
* This is the default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts) using the name `myNewHelmChart`.  

## hpa.yaml  

> More on the <font color="green">HorizontalPodAutoscaler</font> can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/).  

The <font color="green">HorizontalPodAutoscaler</font> is represented by the file <font color="green">hpa.yaml</font> and is located in the `helm/templates` directory. <font color="green">HorizontalPodAutoscaler</font> is instrumental in facilitating scaling via the addition of - or removal of - pods.  

An example file:  

```
{{- if .Values.autoscaling.enabled }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: {{ include "myNewHelmChart.fullname" . }}
  labels:
    {{- include "myNewHelmChart.labels" . | nindent 4 }}
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{ include "myNewHelmChart.fullname" . }}
  minReplicas: {{ .Values.autoscaling.minReplicas }}
  maxReplicas: {{ .Values.autoscaling.maxReplicas }}
  metrics:
    {{- if .Values.autoscaling.targetCPUUtilizationPercentage }}
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: {{ .Values.autoscaling.targetCPUUtilizationPercentage }}
    {{- end }}
    {{- if .Values.autoscaling.targetMemoryUtilizationPercentage }}
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: {{ .Values.autoscaling.targetMemoryUtilizationPercentage }}
    {{- end }}
{{- end }}
```  
* This is the default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts) using the name `myNewHelmChart`.  

## values.yaml  

The file <font color="green">values.yaml</font> is located in the `helm` directory and houses all variables that are referenced in used in other helm-related files.  


```
# Default values for myNewHelmChart.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: nginx
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: ""

podAnnotations: {}

podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80

nodeSelector: {}

tolerations: []

affinity: {}
```  
* This is the default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts) using the name `myNewHelmChart`.  


