---
title: "Montar um volume de Arquivos do Azure em Instâncias de Contêiner do Azure"
description: "Saiba como montar um volume de Arquivos do Azure para persistir o estado com Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41c3a449b39d6ef77e1dd0cf10699f8debcad475
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure

Por padrão, as Instâncias de Contêiner do Azure são sem monitoração de estado. Se o contêiner parar ou falhar, todo o seu estado será perdido. Para persistir o estado além do tempo de vida do contêiner, você deve montar um volume de um repositório externo. Este artigo mostra como montar um compartilhamento de arquivos do Azure para ser usado com Instâncias de Contêiner do Azure.

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Antes de usar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure, você deve criá-lo. Execute o seguinte script para criar uma conta de armazenamento para hospedar o compartilhamento de arquivos e o próprio compartilhamento. O nome da conta de armazenamento deve ser globalmente exclusivo para que o script adicione um valor aleatório à cadeia de caracteres de base.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Obter detalhes de acesso da conta de armazenamento

Para montar um compartilhamento de arquivos do Azure como um volume nas Instâncias de Contêiner do Azure, você precisa de três valores: o nome da conta de armazenamento, o nome do compartilhamento e a chave de acesso de armazenamento.

Se você usou o script acima, o nome da conta de armazenamento foi criado com um valor aleatório no final. Para consultar a cadeia de caracteres final (incluindo a parte aleatória), use os seguintes comandos:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

O nome do compartilhamento já é conhecido (é *acishare* no script acima), portanto, só falta a chave de conta de armazenamento, que pode ser encontrada usando o seguinte comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Armazenar detalhes de acesso da conta de armazenamento com o Azure Key Vault

As chaves da conta de armazenamento protegem o acesso aos seus dados, portanto, recomendamos armazená-las em um Azure Key Vault.

Crie um cofre de chaves com a CLI do Azure:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

A opção `enabled-for-template-deployment` permite que o Azure Resource Manager efetue pull de segredos do seu cofre de chaves no momento da implantação.

Armazene a chave de conta de armazenamento como um novo segredo no cofre de chaves:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Montar o volume

Montar um compartilhamento de arquivos do Azure como um volume em um contêiner é um processo de duas etapas. Primeiro, você fornece os detalhes do compartilhamento como parte da definição do grupo de contêiner e, em seguida, você especifica como deseja o volume montado em um ou mais dos contêineres do grupo.

Para definir os volumes que você deseja disponibilizar para montagem, adicione uma matriz de `volumes` à definição do grupo de contêiner no modelo do Azure Resource Manager e, em seguida, faça referência a eles na definição dos contêineres individuais.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

O modelo inclui o nome da conta de armazenamento e a chave como parâmetros, os quais podem ser fornecidos em um arquivo de parâmetros separado. Para preencher o arquivo de parâmetros, você precisará de três valores: o nome da conta de armazenamento, a ID de recurso do seu Azure Key Vault e o nome do segredo do cofre de chaves que você usou para armazenar a chave de armazenamento. Se seguiu as etapas anteriores, você poderá obter esses valores com o seguinte script:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Insira os valores no arquivo de parâmetros:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Implantar o contêiner e gerenciar arquivos

Com o modelo definido, você pode criar o contêiner e montar seu volume usando a CLI do Azure. Supondo que o arquivo de modelo seja denominado *azuredeploy.json* e que o arquivo de parâmetros seja chamado de *azuredeploy.parameters.json*, a linha de comando será:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

Depois que o contêiner for iniciado, você poderá usar o aplicativo Web simples implantado por meio da imagem **seanmckenna/aci-hellofiles** para gerenciar arquivos no compartilhamento de arquivos do Azure no caminho de montagem especificado. Obtenha o endereço IP para o aplicativo Web da seguinte maneira:

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

Você pode usar uma ferramenta como o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com) para recuperar e inspecionar arquivo gravado no compartilhamento de arquivos.

>[!NOTE]
> Para saber mais sobre como usar modelos do Azure Resource Manager, arquivos de parâmetro e sobre a implantação com a CLI do Azure, consulte [implantar recursos com modelos do Resource Manager e com a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Próximas etapas

- Implantar seu primeiro contêiner usando o [início rápido](container-instances-quickstart.md) das Instâncias de Contêiner do Azure
- Saiba mais sobre o [relacionamento entre as Instâncias de Contêiner do Azure e os orquestradores de contêiner](container-instances-orchestrator-relationship.md)
