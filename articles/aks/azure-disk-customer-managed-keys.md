---
title: Usar uma chave gerenciada pelo cliente para criptografar discos do Azure no serviço kubernetes do Azure (AKS)
description: Traga suas próprias chaves (BYOK) para criptografar o sistema operacional AKS e OS discos de dados.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 4b1c311132cc812ccb2bbbc95c4b7414b108008c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499196"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga suas próprias chaves (BYOK) com discos do Azure no serviço kubernetes do Azure (AKS)

O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar na criptografia em repouso tanto para o sistema operacional quanto para os discos de dados para seus clusters AKS. Saiba mais sobre as chaves gerenciadas pelo cliente no [Linux][customer-managed-keys-linux] e no [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Limitações
* O suporte à criptografia de disco de dados é limitado a clusters AKS que executam o kubernetes versão 1,17 e superior.
* A criptografia do sistema operacional e do disco de dados com chaves gerenciadas pelo cliente só pode ser habilitada durante a criação de um cluster AKS.

## <a name="prerequisites"></a>Pré-requisitos
* Você deve habilitar a exclusão reversível e limpar a proteção para *Azure Key Vault* ao usar Key Vault para criptografar discos gerenciados.
* Você precisa do CLI do Azure versão 2.11.1 ou posterior.

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância de Azure Key Vault

Use uma instância de Azure Key Vault para armazenar suas chaves.  Opcionalmente, você pode usar o portal do Azure para [Configurar chaves gerenciadas pelo cliente com Azure Key Vault][byok-azure-portal]

Crie um novo *grupo de recursos*, crie uma nova instância de *Key Vault* e habilite a exclusão reversível e limpe a proteção.  Certifique-se de usar os mesmos nomes de região e grupo de recursos para cada comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Criar uma instância de um DiskEncryptionSet

Substitua *myKeyVaultName* pelo nome do cofre de chaves.  Você também precisará de uma *chave* armazenada em Azure Key Vault para concluir as etapas a seguir.  Armazene sua chave existente no Key Vault criado nas etapas anteriores ou [gere uma nova chave][key-vault-generate] e substitua *MyKeyName* abaixo pelo nome da sua chave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Conceder ao DiskEncryptionSet acesso ao cofre de chaves

Use o DiskEncryptionSet e os grupos de recursos que você criou nas etapas anteriores e conceda ao recurso DiskEncryptionSet acesso ao Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Criar um novo cluster AKS e criptografar o disco do sistema operacional

Crie um **novo grupo de recursos** e cluster AKs e, em seguida, use sua chave para criptografar o disco do sistema operacional. As chaves gerenciadas pelo cliente só têm suporte em versões kubernetes maiores que 1,17. 

> [!IMPORTANT]
> Certifique-se de criar um novo grupo recurso para o cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Quando novos pools de nós são adicionados ao cluster criado acima, a chave gerenciada pelo cliente fornecida durante a criação é usada para criptografar o disco do sistema operacional.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Criptografar o disco de dados do cluster AKS (opcional)
A chave de criptografia de disco do sistema operacional será usada para criptografar o disco de dados se a chave não for fornecida para o disco de dados do v 1.17.2 e você também pode criptografar discos de dados AKS com suas outras chaves.

> [!IMPORTANT]
> Verifique se você tem as credenciais de AKS adequadas. A identidade gerenciada precisará ter acesso de colaborador ao grupo de recursos em que o diskencryptionset é implantado. Caso contrário, você receberá um erro sugerindo que a identidade gerenciada não tem permissões.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Crie um arquivo chamado **byok-Azure-Disk. YAML** que contém as informações a seguir.  Substitua myAzureSubscriptionId, MyResource e myDiskEncrptionSetName por seus valores e aplique o YAML.  Certifique-se de usar o grupo de recursos onde seu DiskEncryptionSet está implantado.  Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando vi ou nano como se estiver trabalhando em um sistema físico ou virtual:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Em seguida, execute esta implantação em seu cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Próximas etapas

Examinar [as práticas recomendadas para segurança de cluster AKs][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions