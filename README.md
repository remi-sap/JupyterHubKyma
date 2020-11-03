# JupyterHubKyma
JupyterHub running on SAP Cloud Platform Kyma Runtime (Kubernetes) with XSUAA OAUTH2 authentication

Exanmple of runing a shared JupyteHub environment that enables XSUAA autenticated users to access.
Also included is the hana-ml python library to simplify connection to SAP Hana with pandas dataframes, as well as providing the ability to push down ML logic to HANA reducing data being copied. 

## Pre Reqs:
* Provision a Kyma environment on SAP Cloud Platform
* Create a namespace e.g. "mlteam"
* Download your Kyma/K8S access kubeconfig.yml and set KUBECONFIG environment variable to point to kubeconfig.yml
> ## windows cmd
>> Set KUBECONFIG=C:\Users\<user>\Downloads\kubeconfig.yml 
> ## windows powershell
>> $env:KUBECONFIG="C:\Users\<user>\Downloads\kubeconfig.yml" 
> ## linux
>> export KUBECONFIG=/<path>/kubeconfig.yml 

* Check the cluster in your new namespace
> kubectl cluster-info  -n mlteam


## STEP 1 - Create Persitant volume claim 
<strong> To be used for storage of Jupyter Notebooks and associated files </strong>  
kubectl replace --force -n mlteam -f deployment_pvc.yaml


## STEP 2 - Create XSUAA service  
<strong> NOTE: Ensure xsapp does not already exist, deleted previous or rename if required </strong>  

Deploy xsuaa service which creates role in SCP CF 
> kubectl replace --force -n mlteam -f deployment_xsuaa.yaml

Manually generate the XSUAA credentials in Kyma  UI 
> Select namespace created (e.g. mlteam)
> Navigate to 'Service Management -> Instances -> xsuaa-jupyterhub-mlteam'  
> Select 'Credentials' Tab  
> Click 'Create Credentials' Button  
> Click on the generated credentials   
> Click 'Decode' button  


## STEP 2 -Deploy JupypterHub and Service access point

Manually update <strong> deployment_jupyterhub.yaml </strong> with
* clientid / secret from Step 2  
* urls to domain specific XSUAA service  
* cluster info  

NOTE:  Deployment may take ~10 minutes as includes a jupyter build step. If you have docker repository you can prepare an docker file / images containing the more time consuming steps. You would then refer to this image in the deployment yaml.  



