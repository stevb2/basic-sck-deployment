A Basic Splunk Connect for Kubernetes (SCK) Deployment as a newcomer. This will help  set up a small Kubernetes cluster and deploy SCK to send logs, metrics, and objects to a local Splunk instance.

## Step 1: Set up a Kubernetes Cluster

1. Install Minikube:
   - Visit the official Minikube website (https://minikube.sigs.k8s.io/docs/start/)
   - Follow the installation instructions for your operating system

2. Start Minikube:
```
minikube start
```

3. Verify the cluster is running:
```bash
kubectl get nodes
```

## Step 2: Install Helm

1. Install Helm following the official documentation (https://helm.sh/docs/intro/install/)

2. Add the Splunk chart repository:
```
helm repo add splunk https://splunk.github.io/splunk-connect-for-kubernetes/
```

## Step 3: Set up a local Splunk instance

1. Download and install Splunk Enterprise from the official website
2. Start Splunk and create the following indexes:
   - splunk_metrics (for metrics data)
   - k8s_logs (for log data)
   - k8s_metadata (for Kubernetes objects)

3. Create an HTTP Event Collector (HEC) token:
   - In Splunk Web, go to Settings > Data Inputs > HTTP Event Collector
   - Click "New Token" and follow the wizard
   - Make note of the token value

## Step 4: Configure SCK

1. Get the SCK values file:
   ```
   helm show values splunk/splunk-connect-for-kubernetes > values.yaml
   ```

	1. Edit the values.yaml file: Use an online YAML validator like [http://www.yamllint.com/](http://www.yamllint.com/) to check your entire values.yaml file for syntax errors.
   - Set the Splunk HEC URL (e.g., http://your-splunk-ip:8088)
   - Add your HEC token
   - Configure the index names you created earlier

## Step 5: Deploy SCK

1. Install SCK using Helm:
   ```
   # helm uninstall my-sck 
   # helm upgrade my-sck -f values.yaml splunk/splunk-connect-for-kubernetes
   helm install my-sck -f values.yaml splunk/splunk-connect-for-kubernetes
   ```
![Pasted image 20241010164411](https://github.com/user-attachments/assets/34d3a121-3d24-4835-b02b-7d45bac58dbc)

 - See Notes - [[Common Kubernetes Errors]]

2. Verify the deployment:
```powershell
PS C:\Users\Owner> kubectl get pods
NAME                                                   READY   STATUS    RESTARTS   AGE
my-sck-splunk-kubernetes-logging-xgqdg                 1/1     Running   0          68s
my-sck-splunk-kubernetes-metrics-agg-bdf9f88b6-qc5hb   1/1     Running   0          68s
my-sck-splunk-kubernetes-metrics-gtrht                 1/1     Running   0          68s
my-sck-splunk-kubernetes-objects-7754744b46-jxhzc      1/1     Running   0          68s
```
## Step 6: Generate some sample data


1. Deploy a sample application:
```powershell
PS C:\Users\Owner> kubectl create deployment nginx --image=nginx
deployment.apps/nginx created
```

2. Expose the deployment:
```powershell
PS C:\Users\Owner> kubectl expose deployment nginx --port=80 --type=NodePort
service/nginx exposed
```

## Step 7: Verify data in Splunk

1. Log in to your Splunk instance
2. Run searches in each index to verify data is being ingested:
   ```
   index=splunk_metrics
   index=k8s_logs
   index=k8s_metadata
   ```

This basic deployment will give you a good starting point to explore SCK. As you become more comfortable, you can explore advanced features like custom annotations, performance tuning, and creating Splunk dashboards to visualize your Kubernetes data.

Citations:
[1] https://minikube.sigs.k8s.io/docs/tutorials/kubernetes_101/module1/
[2] https://www.civo.com/academy/kubernetes-setup/install-minikube
[3] https://mindmajix.com/splunk-connect-for-kubernetes
[4] https://github.com/splunk/splunk-connect-for-kubernetes
[5] https://community.splunk.com/t5/Splunk-Enterprise/Sending-logs-from-kubernetes-to-on-prem-Splunk-Enterprise/m-p/635258
[6] https://docs.splunk.com/Documentation/ITSI/4.19.1/Entity/SKCIntegration
