---
title: Criar uma StorageClass dos Arquivos do Azure no Red Hat OpenShift no Azure 4
description: Saiba como criar um StorageClass de arquivos do Azure no Red Hat OpenShift do Azure
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: toa, openshift, AZ aro, Red Hat, CLI, arquivo do Azure
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 201ec3293943f53179bcabde45259d15ce6208a6
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901276"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Criar uma StorageClass dos Arquivos do Azure no Red Hat OpenShift no Azure 4

Neste artigo, você criará um StorageClass para o Azure Red Hat OpenShift 4 que provisiona dinamicamente o armazenamento de ReadWriteMany (RWX) usando os arquivos do Azure. Você aprenderá a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Criar um StorageClass do Azure Red Hat OpenShift 4 com o provisionamento de arquivo do Azure

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

Implantar um cluster do Azure Red Hat OpenShift 4 em sua assinatura, confira [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Configurar a conta de armazenamento do Azure

Esta etapa criará um grupo de recursos fora do grupo de recursos do cluster do Azure Red Hat OpenShift (toa). Esse grupo de recursos conterá os compartilhamentos de arquivos do Azure criados pelo provisionamento dinâmico do Azure Red Hat OpenShift.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Configurar permissões
### <a name="set-resource-group-permissions"></a>Definir permissões de grupo de recursos

A entidade de serviço toa requer a permissão ' listKeys ' no novo grupo de recursos da conta de armazenamento do Azure. Atribua a função ' colaborador ' para conseguir isso.

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Definir permissões de cluster toa

A conta de serviço do associador de volume persistente do OpenShift exigirá a capacidade de ler segredos. Crie e atribua uma função de cluster OpenShift para conseguir isso.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Criar StorageClass com o provisionamento de arquivos do Azure

Esta etapa criará um StorageClass com um provisionamento de arquivos do Azure. No manifesto do StorageClass, os detalhes da conta de armazenamento são necessários para que o cluster de toa saiba que você deve examinar uma conta de armazenamento fora do grupo de recursos atual.

Durante o provisionamento de armazenamento, um segredo nomeado por secretname é criado para as credenciais de montagem. Em um contexto multilocação, é altamente recomendável definir o valor para secretNamespace explicitamente, caso contrário, as credenciais da conta de armazenamento podem ser lidas por outros usuários.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  secretNamespace: kube-system
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Alterar o StorageClass padrão (opcional)

O StorageClass padrão na toa é chamado de Managed-Premium e usa o provisionamento de disco do Azure. Altere isso emitindo comandos de patch em relação aos manifestos StorageClass.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Verificar o arquivo do Azure StorageClass (opcional)

Crie um novo aplicativo e atribua o armazenamento a ele.

```bash
oc new-project azfiletest
oc new-app -template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
O arquivo de test.txt também será visível por meio do Gerenciador de Armazenamento no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou o armazenamento persistente dinâmico usando arquivos de Microsoft Azure e o Azure Red Hat OpenShift 4. Você aprendeu a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Configurar um StorageClass em um cluster do Azure Red Hat OpenShift 4 usando o provisionamento de arquivos do Azure

Avance para o próximo artigo para saber mais sobre os recursos com suporte do Azure Red Hat OpenShift 4.

* [Política de suporte do Red Hat OpenShift no Azure](support-policies-v4.md)
