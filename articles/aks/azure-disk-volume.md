---
title: Criar um volume estático para pods no AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar manualmente um volume com discos do Azure para uso com um pod no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: d44c8a7241308c26a3f1148ec70a7a5730dd0c89
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900855"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar manualmente e usar um volume com o discos do Azure no AKS (Serviço de Kubernetes do Azure)

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Se um único pod precisar de acesso ao armazenamento, você poderá usar os discos do Azure para apresentar um volume nativo para o uso do aplicativo. Este artigo mostra como criar um compartilhamento de disco do Azure manualmente e anexá-lo a um pod no AKS.

> [!NOTE]
> Um disco do Azure só pode ser montado em um único pod por vez. Se for necessário compartilhar um volume persistente em vários pods, use [Arquivos do Azure][azure-files-volume].

Para obter mais informações sobre o Kubernetes, veja [Opções de armazenamento para aplicativos no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Criar um disco do Azure

Quando você cria um disco do Azure para uso com o AKS, pode criar o recurso de disco no grupo de recursos **nó** . Essa abordagem permite que o cluster do AKS acesse e gerencie o recurso de disco. Se, em vez disso, você criar o disco em um grupo de recursos separado, deverá conceder a entidade de serviço do AKS (Serviço de Kubernetes do Azure) para seu cluster a função `Contributor` para o grupo de recursos do disco. Como alternativa, você pode usar a identidade gerenciada atribuída pelo sistema para permissões em vez da entidade de serviço. Para obter mais informações, confira [Usar identidades gerenciadas](use-managed-identity.md).

Neste artigo, crie o disco no grupo de recursos do nó. Primeiro, obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o parâmetro de consulta `--query nodeResourceGroup`. O exemplo a seguir obtém o grupo de recursos do nó do nome do cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup* :

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um disco usando o comando [az disk create][az-disk-create]. Especifique o nome de grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso de disco, como *myAKSDisk* . O exemplo a seguir cria um disco de *20* GiB e gera a ID do disco uma vez criado. Se você precisar criar um disco para uso com contêineres do Windows Server, adicione o `--os-type windows` parâmetro para formatar corretamente o disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Os discos do Azure são cobrados por SKU de um tamanho específico. Essas SKUs variam de 32GiB para discos S4 ou P4 para 32TiB para discos S80 ou P80 (em versão prévia). A taxa de transferência e o desempenho de IOPS de um disco gerenciado Premium dependem da SKU e do tamanho da instância dos nós no cluster do AKS. Confira [Preços e desempenho do Managed Disks][managed-disk-pricing-performance].

A ID de recurso de disco é exibida depois que o comando foi concluído com êxito, conforme mostra o seguinte exemplo de saída. Essa ID do disco é usada para montar o disco na próxima etapa.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montar o disco como volume

Para montar o disco do Azure em seu Pod, configure o volume na especificação do contêiner. Crie um novo arquivo chamado `azure-disk-pod.yaml` com o conteúdo a seguir. Atualize `diskName` com o nome do disco criado na etapa anterior, e `diskURI` com a ID do disco mostrada na saída do comando disk create. Se desejado, atualize o `mountPath`, que é o caminho no qual o disco do Azure é montado no pod. Para contêineres do Windows Server, especifique um *mountPath* usando a convenção de caminho do Windows, como *“D:”* .

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
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Use o comando `kubectl` para criar o pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Agora você tem um Pod em execução com um disco do Azure montado no `/mnt/azure`. Você pode usar o `kubectl describe pod mypod` para verificar se o disco foi montado com êxito. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, veja [Práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

Para obter mais informações sobre os clusters do AKS que interagem com os discos do Azure, veja o [Plugin do Kubernetes para Discos do Azure][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
