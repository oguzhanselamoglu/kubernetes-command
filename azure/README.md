## Azure cli kurulum
- [Azure cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli) `Adresinden kurulum yapılır`


## Login
* Azure portal Login ekranını acar browser üzerinden işleme devam edilir
    az login
* Browser açmadan user ve password bilgileriyle cli üzerinden devam edilir
    az login -u <username> -p <password>

## Proje bazlı ilerlediğim için isimlendirmede proje ismi xxx olacak şekilde ilerledim

## Resource group oluşturma
-> az group create --name xxxResourceGroup --location westeurope

## Azure Container Registry Oluşturma
-> az acr create --resource-group xxxResourceGroup --name xxxacr --sku Basic
-> az acr update -n xxxacr --admin-enabled true

## Container registry ye Login olma
-> az acr login --name xxxacr

## Login server address
-> az acr list --resource-group xxxResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
