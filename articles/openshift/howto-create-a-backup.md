---
title: Criar um backup de aplicativo de cluster do Azure Red Hat OpenShift 4 usando o Velero
description: Saiba como criar um backup de seus aplicativos de cluster do Red Hat OpenShift do Azure usando o Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: bbfe280ed0b1b562e0f50b23a09ea159750c4a79
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217084"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Criar um backup de aplicativo de cluster do Azure Red Hat OpenShift 4

Neste artigo, você irá preparar seu ambiente para criar um backup de aplicativo de cluster do Azure Red Hat OpenShift 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Criar um backup de aplicativo do Azure Red Hat OpenShift 4

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

### <a name="install-velero"></a>Instalar o Velero

Para [instalar](https://velero.io/docs/main/basic-install/) o Velero em seu sistema, siga o processo recomendado para seu sistema operacional.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Configurar a conta de armazenamento do Azure e o contêiner de BLOB

Esta etapa criará um grupo de recursos fora do grupo de recursos do cluster de toa.  Esse grupo de recursos permitirá que os backups persistem e possam restaurar aplicativos para novos clusters.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Definir permissões para Velero

### <a name="create-service-principal"></a>Criar uma entidade de serviço

O Velero precisa de permissões para fazer backups e restaurações. Ao criar uma entidade de serviço, você está concedendo permissão ao Velero para acessar o grupo de recursos que você define na etapa anterior. Esta etapa obterá o grupo de recursos do cluster:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Instalar o Velero no cluster do Azure Red Hat OpenShift 4

Esta etapa instalará o Velero em seu próprio projeto e as [definições de recursos personalizados](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) necessárias para fazer backups e restaurações com o Velero. Verifique se você fez logon com êxito em um cluster do Azure Red Hat OpenShift v4.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Criar um backup com o Velero

Para criar um backup de aplicativo com o Velero, você precisará incluir o namespace no qual este aplicativo está.  Se você tiver um `nginx-example` namespace e quiser incluir todos os recursos nesse namespace no backup, execute o seguinte comando no terminal:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Você pode verificar o status do backup executando:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Um backup bem-sucedido será apresentado `phase:Completed` e os objetos residirão no contêiner na conta de armazenamento.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Criar um backup com Velero para incluir instantâneos

Para criar um backup de aplicativo com o Velero para incluir os volumes persistentes do seu aplicativo, você precisará incluir o namespace no qual o aplicativo está e incluir o `snapshot-volumes=true` sinalizador ao criar o backup

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Você pode verificar o status do backup executando:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Um backup bem-sucedido com saída `phase:Completed` e os objetos residirão no contêiner na conta de armazenamento.

Para obter mais informações sobre como criar backups e restaurações usando o Velero, consulte [backup OpenShift Resources da maneira nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, foi feito o backup de um aplicativo de cluster do Azure Red Hat OpenShift 4. Você aprendeu a:

> [!div class="checklist"]
> * Criar um backup de aplicativo de cluster OpenShift v4 usando o Velero
> * Criar um backup de aplicativo de cluster OpenShift V4 com instantâneos usando Velero


Avance para o próximo artigo para saber como criar uma restauração do aplicativo de cluster do Red Hat OpenShift 4 do Azure.

* [Criar uma restauração do aplicativo de cluster do Azure Red Hat OpenShift 4](howto-create-a-restore.md)
* [Criar uma restauração do aplicativo de cluster do Azure Red Hat OpenShift 4 incluindo instantâneos](howto-create-a-restore.md)
