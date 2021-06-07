---
title: Usar pools de nós do sistema no serviço kubernetes do Azure (AKS)
description: Saiba como criar e gerenciar pools de nós do sistema no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 9c53cb53517c4696a1bb47c2cb72335979d58d3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178823"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gerenciar pools de nós do sistema no serviço kubernetes do Azure (AKS)

No AKS (serviço kubernetes do Azure), os nós da mesma configuração são agrupados em *pools de nós*. Os pools de nós contêm as VMs subjacentes que executam seus aplicativos. Os pools de nó do sistema e os pools de nó do usuário são dois modos de pool de nó diferentes para seus clusters AKS. Os pools de nós do sistema atendem à principal finalidade de hospedar pods críticos do sistema, como `CoreDNS` e `metrics-server` . Os pools de nó de usuário servem a principal finalidade de hospedar o pods do aplicativo. No entanto, os pods de aplicativo podem ser agendados em pools de nós do sistema se você quiser ter apenas um pool no cluster AKS. Cada cluster AKS deve conter pelo menos um pool de nós do sistema com pelo menos um nó.

> [!Important]
> Se você executar um pool de nós de sistema único para o cluster AKS em um ambiente de produção, recomendamos que você use pelo menos três nós para o pool de nós.

## <a name="before-you-begin"></a>Antes de começar

* Você precisa do CLI do Azure versão 2.3.1 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a pools de nós do sistema.

* Consulte [cotas, restrições de tamanho de máquina virtual e disponibilidade de região no serviço de kubernetes do Azure (AKs)][quotas-skus-regions].
* O cluster AKS deve ser criado com conjuntos de dimensionamento de máquinas virtuais como o tipo de VM e o balanceador de carga SKU *padrão* .
* O nome de um pool de nós pode conter apenas caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres. Para pools de nós do Windows, o comprimento deve ter entre 1 e 6 caracteres.
* Uma versão de API de 2020-03-01 ou superior deve ser usada para definir um modo de pool de nós. Os clusters criados em versões de API anteriores a 2020-03-01 contêm apenas pools de nó de usuário, mas podem ser migrados para conter pools de nós do sistema, seguindo [as etapas do modo de pool de atualização](#update-existing-cluster-system-and-user-node-pools).
* O modo de um pool de nós é uma propriedade necessária e deve ser definido explicitamente ao usar modelos ARM ou chamadas de API direta.

## <a name="system-and-user-node-pools"></a>Pools de nó do usuário e do sistema

Para um pool de nós do sistema, o AKS atribui automaticamente o rótulo **kubernetes.Azure.com/Mode: System** a seus nós. Isso faz com que o AKS prefira o agendamento de pods do sistema em pools de nós que contêm esse rótulo. Este rótulo não impede que você agende pods de aplicativo em pools de nós do sistema. No entanto, recomendamos que você Isole os pods críticos do sistema do pods do aplicativo para evitar que os pods de aplicativos mal configurados ou invasores apaguem acidentalmente os pods do sistema. Você pode impor esse comportamento criando um pool de nós do sistema dedicado. Use o você `CriticalAddonsOnly=true:NoSchedule` para impedir que os pods de aplicativo sejam agendados nos pools de nós do sistema.

Os pools de nó do sistema têm as seguintes restrições:

* Os pools de sistemas osType devem ser Linux.
* Os pools de nó de usuário osType podem ser Linux ou Windows.
* Os pools do sistema devem conter pelo menos um nó e os pools de nós do usuário podem conter zero ou mais nós.
* Os pools de nós do sistema exigem uma SKU de VM de pelo menos 2 vCPUs e 4 GB de memória.
* Os pools de nós do sistema devem dar suporte a pelo menos 30 pods, conforme descrito pela [fórmula de valor mínimo e máximo para pods][maximum-pods].
* Pools de nós especiais exigem pools de nós de usuário.
* Adicionar um pool de nós de sistema adicional ou alterar qual pool de nós é um pool de nós do sistema *não* moverá automaticamente os pods do sistema. O pods do sistema pode continuar a ser executado no mesmo pool de nós, mesmo se você alterá-lo para um pool de nós de usuário. Se você excluir ou reduzir verticalmente um pool de nós executando pods do sistema que antes era um pool de nós do sistema, esses pods de sistema serão reimplantados com o agendamento preferencial para o novo pool de nós do sistema.

Você pode executar as seguintes operações com pools de nós:

* Criar um pool de nós do sistema dedicado (prefira o agendamento de pods do sistema em pools de nós de `mode:system` )
* Altere um pool de nós do sistema para ser um pool de nós de usuário, desde que você tenha outro pool de nós do sistema para assumir seu lugar no cluster AKS.
* Altere um pool de nós de usuário para ser um pool de nós do sistema.
* Excluir pools de nós de usuário.
* Você pode excluir pools de nós do sistema, desde que você tenha outro pool de nós do sistema para assumir seu lugar no cluster AKS.
* Um cluster AKS pode ter vários pools de nó de sistema e requer pelo menos um pool de nós do sistema.
* Se você quiser alterar várias configurações imutáveis em pools de nós existentes, poderá criar novos pools de nós para substituí-los. Um exemplo é adicionar um novo pool de nós com uma nova configuração maxPods e excluir o pool de nós antigo.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Criar um novo cluster AKS com um pool de nós do sistema

Ao criar um novo cluster AKS, você cria automaticamente um pool de nós do sistema com um único nó. O padrão do pool de nós inicial é um modo do tipo System. Quando você cria novos pools de nós com `az aks nodepool add` , esses pools de nós são pools de nó de usuário, a menos que você especifique explicitamente o parâmetro de modo.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [az aks create][az-aks-create] para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um pool de sistema dedicado que contém um nó. Para suas cargas de trabalho de produção, verifique se você está usando pools de nó do sistema com pelo menos três nós. Essa operação pode levar vários minutos para ser concluída.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Adicionar um pool de nós do sistema dedicado a um cluster AKS existente

> [!Important]
> Não é possível alterar o nó para os comeios por meio da CLI após a criação do pool de nós.

Você pode adicionar um ou mais pools de nós do sistema a clusters AKS existentes. É recomendável agendar o pods do aplicativo nos pools de nós do usuário e dedicar pools de nós do sistema a apenas pods críticos do sistema. Isso impede que os pods de aplicativos invasores Apaguem o pods do sistema acidentalmente. Impor esse comportamento com o `CriticalAddonsOnly=true:NoSchedule` [][aks-taints] seu pool de nós do sistema. 

O comando a seguir adiciona um pool de nós dedicados do sistema de tipos de modo com uma contagem padrão de três nós.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Mostrar detalhes para o pool de nós

Você pode verificar os detalhes do seu pool de nós com o comando a seguir.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Um modo do tipo **System** é definido para pools de nós do sistema, e um modo do tipo **User** é definido para pools de nós do usuário. Para um pool do sistema, verifique se o seu aplicativo está definido como `CriticalAddonsOnly=true:NoSchedule` , o que impedirá os pods dos aplicativos agendados neste pool de nós.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Atualizar o sistema de cluster existente e os pools de nó do usuário

> [!NOTE]
> Uma versão de API de 2020-03-01 ou superior deve ser usada para definir um modo de pool de nós do sistema. Os clusters criados em versões de API anteriores a 2020-03-01 contêm apenas pools de nó de usuário como resultado. Para receber a funcionalidade e os benefícios do pool de nós do sistema em clusters mais antigos, atualize o modo de pools de nós existentes com os seguintes comandos na versão mais recente do CLI do Azure.

Você pode alterar os modos para os pools de nó do usuário e do sistema. Você poderá alterar um pool de nós do sistema para um pool de usuários somente se outro pool de nós do sistema já existir no cluster AKS.

Esse comando altera um pool de nós do sistema para um pool de nós do usuário.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Esse comando altera um pool de nós de usuário para um pool de nós do sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Excluir um pool de nós do sistema

> [!Note]
> Para usar pools de nó de sistema em clusters AKS antes da versão de API 2020-03-02, adicione um novo pool de nós do sistema e, em seguida, exclua o pool de nós padrão original.

Você deve ter pelo menos dois pools de nós do sistema no cluster AKS antes de poder excluir um deles.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para excluir o cluster, use o comando [AZ Group Delete][az-group-delete] para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar e gerenciar pools de nós do sistema em um cluster AKS. Para obter mais informações sobre como usar vários pools de nós, consulte [usar vários pools de nós][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
