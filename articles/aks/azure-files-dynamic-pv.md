---
title: Criar dinamicamente um volume de Arquivos para vários pods no AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar dinamicamente um volume persistente com Arquivos do Azure para uso com vários pods simultâneos no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665982"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Crie e use, dinamicamente, um volume persistente com Arquivos do Azure no AKS (Serviço de Kubernetes do Azure)

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, você poderá usar os arquivos do Azure para se conectar usando o [protocolo SMB][smb-overview]. Este artigo mostra como criar dinamicamente um compartilhamento de Arquivos do Azure para uso por vários pods em um cluster do AKS (Serviço de Kubernetes do Azure).

Para obter mais informações sobre volumes kubernetes, consulte [Opções de armazenamento para aplicativos em AKs][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento é criada automaticamente no [grupo de recursos do nó][node-resource-group] para uso com a classe de armazenamento para manter os compartilhamentos de arquivos do Azure. Escolha a seguinte [redundância de armazenamento do Azure][storage-skus] para *skuName*:

* *Standard_LRS*: armazenamento com redundância local (LRS)
* *Standard_GRS*: armazenamento com redundância geográfica (GRS)
* *Standard_RAGRS*: armazenamento com redundância geográfica e acesso de leitura padrão (RA-GRS)

> [!NOTE]
> Os arquivos do Azure dão suporte ao armazenamento Premium em clusters AKS que executam o kubernetes 1,13 ou superior.

Para obter mais informações sobre classes de armazenamento kubernetes para arquivos do Azure, consulte [classes de armazenamento kubernetes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie-o manifesto de exemplo a seguir. Para obter mais informações sobre o mountoptions, consulte a seção [Opções de montagem][mount-options] .

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Criar uma associação e função de cluster

Os clusters AKS usam o controle de acesso baseado em função (RBAC) do Kubernetes para limitar as ações que podem ser executadas. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [usando a autorização do RBAC][kubernetes-rbac].

Para permitir que a plataforma do Azure crie os recursos de armazenamento necessários, crie um *ClusterRole* e *ClusterRoleBinding*. Crie um arquivo chamado `azure-pvc-roles.yaml` e o copie no YAML a seguir:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Atribua as permissões com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. O YAML a seguir pode ser usado para criar uma declaração de volume persistente de *5 GB* de tamanho com *acesso ReadWriteMany*. Para obter mais informações sobre modos de acesso, consulte a documentação de [volume persistente do kubernetes][access-modes] .

Agora, crie um arquivo chamado `azure-file-pvc.yaml` e copie no YAML a seguir. Certifique-se de que o *storageClassName* corresponde à classe de armazenamento criada na última etapa:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Crie a declaração de volume persistente com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado. Você pode usar o comando [Get do kubectl][kubectl-get] para exibir o status do PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

O YAML a seguir cria um pod que usa a declaração de volume persistente *azurefile* para montar o compartilhamento de arquivos do Azure no caminho */mnt/azure*. Para contêineres do Windows Server (atualmente em visualização em AKS), especifique um *mountPath* usando a Convenção de caminho do Windows, como *: '* .

Crie um arquivo chamado `azure-pvc-files.yaml` e copie no YAML a seguir. Certifique-se de que *claimName* corresponda ao PVC criado na última etapa.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
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
        claimName: azurefile
```

Crie o Pod com o comando [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com seu compartilhamento de arquivos do Azure montado no diretório */mnt/azure*. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
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
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

Os valores padrão *fileMode* e *dirMode* diferem entre as versões do Kubernetes, conforme descrito na tabela a seguir.

| versão | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v.1.8.6 ou superior | 0755 |
| v1.9.0 | 0700 |
| v.1.9.1 ou superior | 0755 |

Se estiver usando um cluster da versão 1.8.5 ou superior e criar dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Se usar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o *runAsUser* valor definido como *0*. Para obter mais informações sobre o contexto de segurança Pod, consulte [configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

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