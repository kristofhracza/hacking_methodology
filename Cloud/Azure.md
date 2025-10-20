# Components

> [!faq] Variable Definitions
> Some variables that are referenced in the examples below are either: hard coded, queries at runtime or passed through the CLI.
> Please refer to the [Commands](#commands) section for obtaining said variables!


## Logic App
This is the overall "container" for everything. This stores all other components and can be triggered by a _Recurrence Trigger_.

## Azure Container Registry (ACR)
The ACR stores the Docker image as a Docker image alone cannot be uploaded to Azure.
- **Example Use Case:** Holds a Docker image so Azure services can fetch it.
### Example Instance
```bash
az acr create -g "$RG" -n "$ACR_NAME" --sku Basic --location "$LOC"
```

## Docker Container / Image
This stores all our scripts and tools, and this is where the scanning and all operations will take place. We will create the image locally and push it to the _ACR_.
    
## Key Vault
This is where we store all the environment variables such as SQL creds _(yes, this is safe)_. _Container Jobs_ can query these variables with _Managed Identity_
- **Example Use Case:** "secrets" will be queried _(through a Container Job)_ by any script that needs access to the aforementioned credentials.
### Example Instance
```bash
# The role is "Key Vault Secrets Officer" because it needs to be able to read and write
az role assignment create \
  --role "Key Vault Secrets Officer" \
  --assignee "$CURRENT_USER" \
  --scope "/subscriptions/$SUB_ID/resourceGroups/$RG"
  
# Adding secrets
az keyvault secret set --vault-name $VAULT_NAME --name $VAR_NAME --value "$1"
az keyvault secret set --vault-name $VAULT_NAME --name $VAR_NAME --value "$2"
```

## Managed Identity
Any Azure resource can be assigned to an identity. Usually used for fetching creds and secrets.
- **Example Use Case:** Assigning _Key Vault Secrets User_ to be able to read secrets in the vault and _AcrPull_ to pull the Docker image from the ACR. In our case, it is assigned to the _ACI_.
### Example Instance:
*Some ACR rules will be defined here as they need newly setup identity*
```bash
# Assign roles to:
# - Vault: to be able to read secrets
# - ACR: to pull docker image
az role assignment create \
  --assignee-object-id "$IDENTITY_PRINCIPAL_ID" \
  --assignee-principal-type ServicePrincipal \
  --role "Key Vault Secrets User" \
  --scope "$KEYVAULT_ID"
  
# ACR role
az role assignment create \
  --assignee-object-id "$IDENTITY_PRINCIPAL_ID" \
  --assignee-principal-type ServicePrincipal \
  --role "AcrPull" \
  --scope "$SCOPE"
```

##  Azure Container Instance (ACI) 
Pulls a docker image from the _ACR_.
- **Example Use Case:** The Logic App will trigger the ACI which will pull and execute the Docker image from the ACR.
### Example Instance:
```bash
# Creaing the ACI
az container create \
  --resource-group "$RG" \
  --name "$ACI_NAME" \
  --image "$FULL_IMAGE" \
  --assign-identity "$IDENTITY_RESOURCE_ID" \
  --registry-login-server "$ACR_NAME.azurecr.io" \
  --registry-username "$ACR_USERNAME" \
  --registry-password "$ACR_PASSWORD" \
  --os-type Linux \
  --restart-policy Never \
  --cpu 1 --memory 1.5 \
  --environment-variables \
      VAR_NAME1="$VAR_NAME1" \
      VAR_NAME2="$VAR_NAME2" \
```

# Create Environment In Order:
1. Create the **ACR**
2. Push **Docker Image** to Azure ACR
3. Create the **Key Vault**
	1.  Give roles
	2. Set secrets
4. Create **ACI**
	1. Get ACR creds and all other info needs for assignment. (For including it as environment variable)

# Commands
```bash
# Identity name, ID and Principal ID
az identity list -g "$RG" --query "[0].name" -o tsv
az identity show -g "$RG" -n "$IDENTITY_NAME" --query id -o tsv
az identity show -g "$RG" -n "$IDENTITY_NAME" --query principalId -o tsv


# ACI
## List containers
az container list

## Container logs
az container logs --resource-group <RG_NAME> --name <ACI_NAME>

## Start process chain from ACI
az container start --resource-group <RG_NAME> --name <ACI_NAME>
### If inaccessible, run: az acr update -n <ACR_NAME> --admin-enabled true


# ACR
## Scope
az acr show -n "$ACR_NAME" --query id -o tsv
az acr update -n "$ACR_NAME" --admin-enabled true

## Creds
az acr credential show -n "$ACR_NAME" --query username -o tsv
az acr credential show -n "$ACR_NAME" --query passwords[0].value -o tsv

## List containers
az acr repository list --name

## List manifests
az acr manifest list --name <your-registry-name> --repository <repository-name>


# Key Vault
## Add secrets
az keyvault secret set --vault-name $VAULT_NAME --name $VAR_NAME --value "$1"

## Get secrets
az keyvault secret show --name $VAR_NAME$ --vault-name "$<VAULT_NAME>" --query value -o tsv
```