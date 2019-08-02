---
title: Criar dinamicamente um volume de Disco para vários pods no AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar dinamicamente um volume persistente com discos do Azure para uso com vários pods simultâneos no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 0641d613da86aeffa0c4abb0f82ce93c38283156
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616089"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar e usar dinamicamente um volume persistente com discos do Azure no AKS (Serviço de Kubernetes do Azure)

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. Este artigo mostra como criar dinamicamente volumes persistentes com discos do Azure para uso por um único pod em um cluster do AKS (Serviço de Kubernetes do Azure).

> [!NOTE]
> Um disco do Azure somente pode ser montado com o *modo de Aceso* tipo *ReadWriteOnce*, que o torna disponível para apenas um único pod no AKS. Se você precisar compartilhar um volume persistente em vários pods, use [os arquivos do Azure][azure-files-pvc].

Para obter mais informações sobre volumes kubernetes, consulte [Opções de armazenamento para aplicativos em AKs][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Interno de classes de armazenamento

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre classes de armazenamento kubernetes, consulte [classes de armazenamento kubernetes][kubernetes-storage-classes].

Cada cluster AKS inclui duas classes de armazenamento criadas previamente, ambas configuradas para funcionarem com discos do Azure:

* A classe de armazenamento *padrão* provisiona um disco standard do Azure.
    * O armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.
* A classe de armazenamento *managed-premium* provisiona um disco premium do Azure.
    * Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. Se os nós do AKS no cluster usarem o armazenamento premium, selecione a classe *managed-premium*.
    
Essas classes de armazenamento padrão não permitem que você atualize o tamanho do volume depois de criado. Para habilitar essa capacidade, adicione a linha *allowVolumeExpansion: true* a uma das classes de armazenamento padrão ou crie sua própria classe de armazenamento personalizada. Você pode editar uma classe de armazenamento existente usando `kubectl edit sc` o comando. Para obter mais informações sobre classes de armazenamento e criação de youor, consulte [Opções de armazenamento para aplicativos em AKs][storage-class-concepts].

Use o comando [kubectl Get SC][kubectl-get] para ver as classes de armazenamento criadas previamente. A exemplo a seguir mostra as classes de armazenamento criadas previamente disponíveis dentro de um cluster do AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Declarações de volume persistentes são especificadas em GiB, mas os discos gerenciados do Azure são cobrados por SKU para um tamanho específico. Essas SKUs variam de 32GiB para discos S4 ou P4 para 32TiB para discos S80 ou P80 (em versão prévia). A taxa de transferência e o desempenho de IOPS de um disco gerenciado Premium dependem da SKU e do tamanho da instância dos nós no cluster do AKS. Para obter mais informações, consulte [preços e desempenho de Managed disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Um PVC (declaração de volume persistente) é usado para provisionar automaticamente o armazenamento com base em uma classe de armazenamento. Nesse caso, um PVC pode usar uma das classes de armazenamento pré-criadas para criar um disco gerenciado standard ou premium do Azure.

Crie um arquivo chamado `azure-premium.yaml` e copie-o para o manifesto a seguir. A declaração solicita um disco chamado `azure-managed-disk` que tenha *5 GB* de tamanho com acesso *ReadWriteOnce*. A classe de armazenamento *premium gerenciado* é especificada como a classe de armazenamento.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Para criar um disco que use o armazenamento padrão, use `storageClassName: default` em vez de *premium gerenciado*.

Crie a declaração de volume persistente com o comando [kubectl Apply][kubectl-apply] e especifique seu arquivo *Azure-Premium. YAML* :

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

Quando a declaração do volume persistente tiver sido criada e o disco tiver sido provisionado com êxito, um pod poderá ser criado com acesso ao disco. O manifesto a seguir cria um pod NGINX básico que usa a declaração de volume persistente chamada *azure-managed-disk* para montar o disco do Azure no caminho `/mnt/azure`. Para contêineres do Windows Server (atualmente em visualização em AKS), especifique um *mountPath* usando a Convenção de caminho do Windows, como *: '* .

Crie um arquivo chamado `azure-pvc-disk.yaml` e copie-o para o manifesto a seguir.

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
        claimName: azure-managed-disk
```

Crie o Pod com o comando [kubectl Apply][kubectl-apply] , conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Agora você tem um pod em execução com o disco do Azure montado no diretório `/mnt/azure`. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`, conforme mostrado no exemplo condensado a seguir:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Fazer backup de um volume persistente

Para fazer backup dos dados no seu volume persistente, crie um instantâneo do disco gerenciado para o volume. Em seguida, você pode usar esse instantâneo para criar um disco restaurado e anexá-lo a pods como um meio de restauração dos dados.

Primeiro, obtenha o nome do volume com o comando `kubectl get pvc`, como para o PVC chamado *azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Esse nome de volume forma o nome do disco do Azure subjacente. Consulte a ID do disco com [AZ Disk List][az-disk-list] e forneça o nome do volume de PVC, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Use a ID do disco para criar um disco de instantâneo com [AZ snapshot Create][az-snapshot-create]. O exemplo a seguir cria um instantâneo denominado *pvcSnapshot* no mesmo grupo de recursos que o cluster do AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Você poderá encontrar problemas de permissão se criar instantâneos e restaurar discos em grupos de recursos aos quais o cluster do AKS não tenha acesso.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Dependendo da quantidade de dados no disco, poderão ser necessários alguns minutos para criar o instantâneo.

## <a name="restore-and-use-a-snapshot"></a>Restaurar e usar um instantâneo

Para restaurar o disco e usá-lo com um pod kubernetes, use o instantâneo como uma origem ao criar um disco com [AZ Disk Create][az-disk-create]. Essa operação preservará o recurso original se você então precisar acessar o instantâneo de dados original. O exemplo a seguir cria um disco chamado *pvcRestored* com base no instantâneo denominado *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Para usar o disco restaurado com um pod, especifique a ID do disco no manifesto. Obtenha a ID do disco com o comando [AZ Disk show][az-disk-show] . O exemplo a seguir obtém a ID do disco para o *pvcRestored* criado na etapa anterior:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Crie um manifesto de pod chamado `azure-restored.yaml` e especifique o URI do disco obtido na etapa anterior. O exemplo a seguir cria um servidor Web NGINX básico, com o disco restaurado montado como um volume em */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Crie o Pod com o comando [kubectl Apply][kubectl-apply] , conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Você pode usar `kubectl describe pod mypodrestored` para exibir detalhes de pod, como no seguinte exemplo condensado que mostra as informações de volume:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

Saiba mais sobre volumes persistentes Kubernetes usando os discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in kubernetes para discos do Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
