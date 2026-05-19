# Day-78----Introduction-to-Helm-and-Chart-Basics

## Task

You have deployed applications with raw Kubernetes manifests -- writing Deployments, Services, ConfigMaps, and Secrets by hand. The AI-BankApp project (https://github.com/TrainWithShubham/AI-BankApp-DevOps, branch feat/gitops) has 12 YAML files in its k8s/ directory. Managing those across dev, staging, and production with slightly different configurations is painful.

Helm is the package manager for Kubernetes. It lets you template, package, version, and deploy Kubernetes applications as reusable units called charts. Today you install Helm, understand chart structure, and deploy your first applications using community charts -- including MySQL, which the AI-BankApp depends on.

## Expected Output

- Helm installed and connected to your Kubernetes cluster
- At least two applications deployed using Helm from public chart repositories
- Understanding of chart structure: templates, values, Chart.yaml
- Helm release management: install, upgrade, rollback, uninstall
- A markdown file: day-78-helm-intro.md

## Challenge Tasks

## Task 1: Understand Helm Concepts

Research and write notes on:

1. What is Helm?

Helm is a package manager for Kubernetes, similar to how apt works in Ubuntu or yum works in RHEL.

Helm helps you:

- Package Kubernetes YAML files into reusable packages called Charts
- Manage applications easily in Kubernetes
- Reuse the same configuration for different environments
- Install, upgrade, rollback, and uninstall applications with simple commands

Without Helm, you manually manage many YAML files.
With Helm, everything becomes organized and automated.

2. Core Concepts of Helm
  
1. Chart

A Chart is a collection of files that describe Kubernetes resources.

Example:

Deployment
Service
ConfigMap
Secret
Ingress

All these files together form one Helm chart.

Example structure:

<img width="318" height="179" alt="image" src="https://github.com/user-attachments/assets/657219ef-d6e9-4c60-b5d1-51656375af0b" />

2. Release

A Release is a running instance of a chart inside a Kubernetes cluster.

Example:

helm install bankapp mychart

Here:

bankapp = release name
mychart = chart name

You can install the same chart multiple times with different release names.

Example:

helm install dev-bankapp mychart
helm install prod-bankapp mychart

3. Repository

A Repository is a place where Helm charts are stored and shared.

Similar to:

DockerHub for Docker images
GitHub for code

Popular Helm repository:

https://charts.bitnami.com/bitnami

Example command:

helm repo add bitnami https://charts.bitnami.com/bitnami

Repositories contain ready-made charts for:

MySQL
Redis
Prometheus
Grafana
ArgoCD

4. Values

Values are configuration settings used to customize a chart.

Example settings:

Replica count
Image version
CPU/Memory limits
Database password

Example values.yaml:

<img width="297" height="189" alt="image" src="https://github.com/user-attachments/assets/f1d90630-8864-43ca-930a-450f53fff3b8" />

You can override values during installation:

helm install myapp mychart --set replicaCount=3

Or use a custom values file:

helm install myapp mychart -f values-prod.yaml

3. Why Helm Over Raw Kubernetes Manifests?

In the AI-BankApp project, the k8s/ folder contains many YAML files.

Example:

deployment.yaml
service.yaml
configmap.yaml
secret.yaml
ingress.yaml

Managing them manually becomes difficult.

Problems with Raw YAML Files
1. Manual Changes

To change the image tag:

image: bankapp:v2

You must manually edit deployment YAML files.

2. Difficult Environment Management

For:

Development
Testing
Production

You may need separate YAML files for each environment.

This increases duplication and confusion.

3. No Easy Rollback

If deployment fails:

Manual rollback is difficult
Previous versions are not tracked automatically

Advantages of Helm
1. Templating

One chart can be used for multiple environments.

Example:

Dev → 1 replica
Prod → 5 replicas

Only values change, templates stay same.

2. Versioning

Helm charts have version numbers.

You can:

Upgrade
Downgrade
Rollback easily

Example:

helm rollback myapp 1
3. Dependencies

A chart can depend on other charts.

Example:

AI-BankApp chart depends on MySQL chart

Helm automatically installs dependencies.

4. Community Charts

Thousands of pre-built charts are available.

Popular examples:

MySQL
Redis
Prometheus
Grafana
ArgoCD

This saves development time.

# Task 2: Install Helm and Explore the AI-BankApp

You need a running Kubernetes cluster. Use any of these:

- Kind (recommended for this block): Use the AI-BankApp's Kind config
- Minikube: minikube start
- Docker Desktop Kubernetes: enable in settings
  
Set up a Kind cluster using the AI-BankApp's config:

<img width="541" height="80" alt="image" src="https://github.com/user-attachments/assets/0872e696-f644-4fcf-89fb-a2bdb821ca90" />

This creates a cluster with 1 control plane and 2 worker nodes.

Install Helm:

<img width="605" height="111" alt="image" src="https://github.com/user-attachments/assets/b470140c-0d9b-4e50-aa9c-fdaf4577e746" />

Confirm Helm can talk to your cluster:

<img width="276" height="56" alt="image" src="https://github.com/user-attachments/assets/13e66c95-e021-4065-aaf0-33e43841b50a" />

Explore the raw manifests you will eventually replace with Helm:

<img width="173" height="38" alt="image" src="https://github.com/user-attachments/assets/ab02eb30-fe06-4c2d-9a1e-c18671aa0917" />

<img width="573" height="77" alt="image" src="https://github.com/user-attachments/assets/57a78a3d-0a99-4c57-acdd-b6b64668a294" />

12 files -- Deployments, Services, ConfigMaps, Secrets, PVCs, HPA, and more. All hardcoded values. On Day 79, you will convert these into a Helm chart.

## Task 3: Deploy MySQL Using a Helm Chart

The AI-BankApp needs MySQL. Instead of applying raw YAML like k8s/mysql-deployment.yml, deploy it with Helm.

Add the Bitnami chart repository:

<img width="520" height="58" alt="image" src="https://github.com/user-attachments/assets/6e12701e-a882-4ff1-903d-8fb65ac4dee6" />

Search for MySQL:

<img width="293" height="40" alt="image" src="https://github.com/user-attachments/assets/58f30da7-ba10-4b62-bace-a5f9aa273134" />

Deploy MySQL with the same config the AI-BankApp expects:

<img width="564" height="165" alt="image" src="https://github.com/user-attachments/assets/7b2b02a4-789c-4239-985d-a6330af48cc0" />

Compare this single command to the raw manifest approach which needs mysql-deployment.yml + secrets.yml + pvc.yml + pv.yml + service.yml. Helm handles all of it.

Check what was created:

<img width="467" height="96" alt="image" src="https://github.com/user-attachments/assets/e0eb724f-9ecb-426d-b8ad-88843f332226" />

Verify MySQL is running:

<img width="592" height="43" alt="image" src="https://github.com/user-attachments/assets/6756bc21-5ea5-4d81-85c4-553efa72dd3a" />

You should see bankappdb in the output.

## Task 4: Customize a Deployment with Values Files

--set works for quick overrides, but real projects use values files.

Create mysql-values.yaml:

<img width="439" height="345" alt="image" src="https://github.com/user-attachments/assets/d990c197-d1b8-489a-a758-840edcf36a19" />

Deploy with the values file:

<img width="466" height="34" alt="image" src="https://github.com/user-attachments/assets/6dc47e67-967b-4cb9-aa99-d474cb1cad89" />

To see all configurable values for a chart:

<img width="388" height="41" alt="image" src="https://github.com/user-attachments/assets/22ea9be9-c8a3-4d4e-82c0-ae082534246c" />

This is your reference for every knob you can turn. Notice how the chart supports metrics, replication, custom init scripts, and dozens more options -- all through values.

Clean up the second release:

<img width="376" height="37" alt="image" src="https://github.com/user-attachments/assets/af0c1d4b-1dca-4f97-819e-453457e2b714" />

# Task 5: Manage Releases -- Upgrade, Rollback, Uninstall

Helm tracks every change as a revision. This lets you upgrade and rollback safely.

Upgrade MySQL to enable metrics:

<img width="415" height="89" alt="image" src="https://github.com/user-attachments/assets/bc7ca6f7-459d-49bb-9ae5-44951be34006" />

Check the revision history:

<img width="386" height="43" alt="image" src="https://github.com/user-attachments/assets/75af73c4-44d1-4c2c-96fa-5645af6c162e" />

You should see revision 1 (original) and revision 2 (metrics enabled).

Rollback to the previous version:

<img width="360" height="44" alt="image" src="https://github.com/user-attachments/assets/ee7a6825-f478-49b6-a5c9-5bd54fe0ff45" />

Check history again:

<img width="339" height="36" alt="image" src="https://github.com/user-attachments/assets/a5397682-52a9-4993-a25a-a56f5715dac9" />

Revision 3 appears -- a rollback to revision 1.

Compare this to raw manifests: With kubectl apply, there is no built-in rollback. You would have to git revert or manually re-apply old YAML. Helm gives you helm rollback out of the box.

# Task 6: Explore a Chart's Structure

Before building your own chart for the AI-BankApp tomorrow, understand what is inside a Helm chart.

Pull the MySQL chart locally:

<img width="371" height="54" alt="image" src="https://github.com/user-attachments/assets/08165dde-c51d-4c03-9158-3ace4e262230" />

You will see:

<img width="563" height="223" alt="image" src="https://github.com/user-attachments/assets/b8aac6cc-d7be-4771-be85-d6d2ada3a845" />

Open templates/primary/statefulset.yaml and look for Go template syntax:

<img width="510" height="56" alt="image" src="https://github.com/user-attachments/assets/26c19e20-70b6-4328-b7cd-b044eb40e48a" />

{{ .Values.primary.replicaCount }} pulls from values.yaml. When you pass --set primary.replicaCount=3, it overrides this value.

Open Chart.yaml:

<img width="522" height="108" alt="image" src="https://github.com/user-attachments/assets/47df7f11-0b81-49b2-9e11-be4f96b31000" />

Now compare the Helm chart approach to the AI-BankApp's raw manifests:

|Aspect|AI-BankApp k8s/mysql-deployment.yml|Bitnami MySQL Helm Chart|
|-------|----------------------------------|-------------------------|
|Secrets|Hardcoded base64 in secrets.yml|Generated and managed by Helm|
|Storage|Manual StorageClass + PVC files|Configured via persistence.size value|
|Replicas|Hardcoded in YAML|primary.replicaCount value|
|Metrics|Not included|metrics.enabled: true|
|Rollback|Manual|helm rollback|

Document: What is the difference between version and appVersion in Chart.yaml?

Clean up:

<img width="297" height="53" alt="image" src="https://github.com/user-attachments/assets/1f35ed5e-a543-459c-8f21-87b02b533e12" />























