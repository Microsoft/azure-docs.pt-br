---
title: Criação dinâmica de compartilhamento do serviço Arquivos do Azure
titleSuffix: Azure Kubernetes Service
description: Saiba como criar dinamicamente um volume persistente com Arquivos do Azure para uso com vários pods simultâneos no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744902"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Crie e use, dinamicamente, um volume persistente com Arquivos do Azure no AKS (Serviço de Kubernetes do Azure)

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, use o serviço Arquivos do Azure para se conectar por meio do [protocolo SMB][smb-overview]. Este artigo mostra como criar dinamicamente um compartilhamento de Arquivos do Azure para uso por vários pods em um cluster do AKS (Serviço de Kubernetes do Azure).

Para obter mais informações sobre o Kubernetes, veja [Opções de armazenamento para aplicativos no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento é criada automaticamente no [grupo de recursos do nó][node-resource-group] para uso com a classe de armazenamento para reter os compartilhamentos do serviço Arquivos do Azure. Escolha a seguinte [redundância de armazenamento do Azure][storage-skus] para *skuName*:

* *Standard_LRS*: armazenamento com redundância local (LRS)
* *Standard_GRS*: armazenamento com redundância geográfica (GRS)
* *Standard_ZRS*: armazenamento com redundância de zona (ZRS) padrão
* *Standard_RAGRS*: armazenamento com redundância geográfica e acesso de leitura padrão (RA-GRS)
* *Premium_LRS* – Armazenamento com redundância local (LRS) premium.
* *Premium_ZRS* -armazenamento com redundância de zona Premium (ZRS)

> [!NOTE]
> O serviço Arquivos do Azure dá suporte ao armazenamento premium em clusters do AKS que executam o Kubernetes 1.13 ou superior, o compartilhamento de arquivo premium mínimo é de 100 GB

Para obter mais informações sobre classes de armazenamento do Kubernetes para o serviço Arquivos do Azure, veja [Classes de armazenamento do Kubernetes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie-o manifesto de exemplo a seguir. Para obter mais informações sobre *mountOptions*, veja a seção [Opções de montagem][mount-options].

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. O YAML a seguir pode ser usado para criar uma declaração de volume persistente de *5 GB* de tamanho com acesso do tipo *ReadWriteMany*. Para obter mais informações sobre os modos de acesso, veja a documentação sobre [Volume persistente de Kubernetes][access-modes].

Agora, crie um arquivo chamado `azure-file-pvc.yaml` e copie no YAML a seguir. Certifique-se de que o *storageClassName* corresponde à classe de armazenamento criada na última etapa:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Se estiver usando a SKU *Premium_LRS* para sua classe de armazenamento, o valor mínimo para *storage* deve ser *100 Gi*.

Crie a declaração de volume persistente com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado. Use o comando [kubectl get][kubectl-get] para exibir o status do PVC:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

O YAML a seguir cria um pod que usa a declaração de volume persistente *My-azurefile* para montar o compartilhamento de arquivos do Azure no caminho */mnt/Azure* . Para contêineres do Windows Server, especifique um *mountPath* usando a convenção de caminho do Windows, como *“D:”* .

Crie um arquivo chamado `azure-pvc-files.yaml` e copie no YAML a seguir. Certifique-se de que *claimName* corresponda ao PVC criado na última etapa.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: my-azurefile
```

Crie o pod com o comando [kubectl apply][kubectl-apply].

```console
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com seu compartilhamento de arquivos do Azure montado no diretório */mnt/azure*. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

O valor padrão para *fileMode* e *dirMode* é *0777* para o Kubernetes versão 1.13.0 e superior. Se estiver criando dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, veja [Práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

Para parâmetros de classe de armazenamento, consulte [provisionamento dinâmico](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Saiba mais sobre volumes persistentes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
