To reproduce:

```
# Install Azure CLI and connect to Azure Stack based on https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-version-profiles-azurecli2

# [Optional] To learn details on how to use AKS Engine see "Usage" in https://github.com/Azure/aks-engine/blob/master/docs/tutorials/deploy.md

# To install Azure Stack AKS Engine use curl command below and unpack content
curl -L -s -f -O https://github.com/Azure/aks-engine/releases/download/v0.38.3/aks-engine-v0.38.3-linux-amd64.tar.gz

# Get the Kubernetes json file

curl -o kubernetes1.15.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azurestack/kubernetes-azurestack-azure-ad.json

# Open the Kubernetes json file and update orchestratorRelease value to 1.15

# To run the sonobuoy tests follow these steps:

# 1. Get the current Azure Stack subscription ID 

$ az login

$ SUBID=`az account list --all -o table |grep True |awk -F '  ' '{print $3}'`

# 2. Deploy the kubernetes cluster

$ aks-engine deploy \
             --api-model kubernetes1.15.json \
             --auth-method client_secret \
             --azure-env AzureStackCloud \
             --location local \
             --identity-system azure_ad \
             --subscription-id $SUBID

# 3. Run below steps on master

$ curl -L -o sonobuoy_0.15.0_linux_amd64.tar.gz \
    https://github.com/heptio/sonobuoy/releases/download/v0.15.0/sonobuoy_0.15.0_linux_amd64.tar.gz

# untar the tarball.
$ sudo tar -xvf sonobuoy_0.15.0_linux_amd64.tar.gz

# Start the tests.
$ ./sonobuoy run

# Wait till sonobuoy runs are complete. You can query using below command or wait for ~60minutes.
$ ./sonobuoy status | grep running

# Retrieve the results. This will create a tar file.
$ ./sonobuoy retrieve

# untar the tarball, then add plugins/e2e/results/{e2e.log,junit_01.xml}