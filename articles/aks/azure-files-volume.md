---
title: Criar manualmente o compartilhamento de arquivos do Azure
titleSuffix: Azure Kubernetes Service
description: Saiba como criar manualmente um volume com Arquivos do Azure para uso com vários pods simultâneos no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 4e009c5de2e24c1b0bd94fb4c11b0c52a3bc378d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609066"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Criar manualmente e usar um volume com o compartilhamento de Arquivos do Azure no AKS (Serviço de Kubernetes do Azure)

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, use o serviço Arquivos do Azure para se conectar por meio do [protocolo SMB][smb-overview]. Este artigo mostra como criar um compartilhamento de arquivos do Azure manualmente e anexá-lo a um pod no AKS.

Para obter mais informações sobre o Kubernetes, veja [Opções de armazenamento para aplicativos no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivo do Azure

Antes de usar os Arquivo do Azure como um volume Kubernetes, você deve criar uma conta de Armazenamento do Azure e o compartilhamento de arquivos. Os comandos a seguir criam um grupo de recursos chamado *myAKSShare*, uma conta de armazenamento e um compartilhamento de arquivos chamado *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Anote o nome da conta de armazenamento e a chave mostrada no final da saída do script. Esses valores são necessários quando você cria o volume do Kubernetes em uma das etapas a seguir.

## <a name="create-a-kubernetes-secret"></a>Criar um segredo do Kubernetes

O Kubernetes precisa de credenciais para acessar o compartilhamento de arquivos criado na etapa anterior. Essas credenciais são armazenadas em um [segredo Kubernetes][kubernetes-secret], que é referenciado quando você cria um pod Kubernetes.

Use o comando `kubectl create secret` para criar o segredo. O exemplo a seguir cria um compartilhamento chamado *azure-secret* e preenche o *azurestorageaccountname* e o *azurestorageaccountkey* da etapa anterior. Para usar uma conta de armazenamento existente do Azure, forneça o nome da conta e a chave.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Montar o compartilhamento de arquivos como um volume embutido
> Observação: a partir de 1.18.15, 1.19.7, 1.20.2, 1.21.0, o namespace secreto no volume embutido `azureFile` só pode ser definido como `default` namespace, para especificar um namespace de segredo diferente, use o exemplo de volume persistente abaixo em vez disso.

Para montar o compartilhamento de arquivos do Azure em seu Pod, configure o volume na especificação do contêiner. Crie um novo arquivo chamado `azure-files-pod.yaml` com o conteúdo a seguir. Se você tiver alterado o nome do compartilhamento de arquivos ou o nome do segredo, atualize o *shareName* e o *secretName*. Caso queira, atualize o `mountPath`, que é o caminho em que os o compartilhamento de arquivos é montado no pod. Para contêineres do Windows Server, especifique um *mountPath* usando a convenção de caminho do Windows, como *“D:”* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Use o comando `kubectl` para criar o pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Agora você tem um pod em execução com um compartilhamento de Arquivos do Azure montado no */mnt/azure*. Você pode usar o `kubectl describe pod mypod` para verificar se o compartilhamento foi montado com êxito. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Montar o compartilhamento de arquivos como um volume persistente
 - Opções de montagem

O valor padrão de *fileMode* e *dirMode* é *0777* para kubernetes versão 1,15 e superior. O exemplo a seguir define *0755* no objeto *PersistentVolume* :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Para atualizar suas opções de montagem, crie um arquivo *azurefile-Mount-Options-PV. YAML* com um *PersistentVolume*. Por exemplo:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Crie um arquivo *azurefile-Mount-Options-PVC. YAML* com um *PersistentVolumeClaim* que usa o *PersistentVolume*. Por exemplo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Use os `kubectl` comandos para criar o *PersistentVolume* e o *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verifique se o *PersistentVolumeClaim* foi criado e associado ao *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Atualize a especificação do contêiner para fazer referência ao seu *PersistentVolumeClaim* e atualize seu Pod. Por exemplo:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, veja [Práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

Para obter mais informações sobre os clusters do AKS que interagem com os Arquivos do Azure, consulte o [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files].

Para parâmetros de classe de armazenamento, consulte [provisionamento estático (Traga seu próprio compartilhamento de arquivos)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
