---
title: Serviço de Kubernetes do Azure (AKS) com SLA de tempo de atividade
description: Saiba mais sobre a oferta opcional de SLA de tempo de atividade para o servidor de API do Serviço de Kubernetes do Azure (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9f8f697da7499d370c96b77e7e543dec9fbafa3e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664088"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA de tempo de atividade do Serviço de Kubernetes do Azure (AKS)

O SLA de tempo de atividade é um recurso opcional para habilitar um SLA com suporte financeiro e superior para um cluster. O SLA de tempo de atividade garante 99,95% de disponibilidade do ponto de extremidade do servidor de API do Kubernetes para clusters que usam [Zona de Disponibilidade][availability-zones] e 99,9% da disponibilidade para clusters que não usam Zonas de Disponibilidade. O AKS usa réplicas de nó mestre em domínios de atualização e de falha para garantir que os requisitos de SLA sejam atendidos.

Os clientes que precisam de um SLA para atender aos requisitos de conformidade ou exigem a extensão de um SLA para seus usuários finais devem habilitar esse recurso. Os clientes com cargas de trabalho críticas que aproveitarão bem um SLA de tempo de atividade mais alto também podem se beneficiar. Usar o recurso de SLA de tempo de atividade com Zonas de Disponibilidade permite uma disponibilidade maior para o tempo de atividade do servidor de API do Kubernetes.  

Os clientes ainda podem criar clusters grátis ilimitados com um objetivo de nível de serviço (SLO) de 99,5% e optar pelo tempo de atividade do SLO ou do SLA que preferirem, conforme necessário.

> [!Important]
> Para clusters com bloqueio de tráfego de saída, consulte [limitar tráfego de saída](limit-egress-traffic.md) para abrir as portas apropriadas.

## <a name="region-availability"></a>Disponibilidade de região

* O SLA de tempo de atividade está disponível em regiões públicas e regiões do Azure governamental em que há [suporte para AKs](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* O SLA de tempo de atividade está disponível para [clusters AKs privados][private-clusters] em todas as regiões públicas em que há suporte para AKs.

## <a name="sla-terms-and-conditions"></a>Termos e condições do SLA

O SLA de tempo de atividade é um recurso pago e habilitado por cluster. O preço do SLA de tempo de atividade é determinado pelo número de clusters discretos e não pelo tamanho de clusters individuais. Você pode ver [Detalhes de preços do SLA de tempo de atividade](https://azure.microsoft.com/pricing/details/kubernetes-service/) para mais informações.

## <a name="before-you-begin"></a>Antes de começar

* Instalar o [CLI do Azure](/cli/azure/install-azure-cli) versão 2.8.0 ou posterior

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Criando um novo cluster com SLA de tempo de atividade

> [!NOTE]
> Atualmente, se você habilitar o SLA de tempo de atividade, não será possível removê-lo de um cluster.

Para criar um novo cluster com o SLA de tempo de atividade, use a CLI do Azure.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Use o [`az aks create`][az-aks-create] comando para criar um cluster AKs. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. Esta operação leva vários minutos para ser concluída:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster. O trecho JSON a seguir mostra a camada paga para a SKU, indicando que o cluster está habilitado com SLA de tempo de atividade:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modificar um cluster existente para usar o SLA de tempo de atividade

Opcionalmente, você pode atualizar seus clusters existentes para usar o SLA de tempo de atividade.

Se você criou um cluster AKS com as etapas anteriores, exclua o grupo de recursos:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Criar um novo grupo de recursos:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Crie um novo cluster e não use o SLA de tempo de atividade:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Use o [`az aks update`][az-aks-update] comando para atualizar o cluster existente:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 O trecho JSON a seguir mostra a camada paga para a SKU, indicando que o cluster está habilitado com SLA de tempo de atividade:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Limpar

Para evitar cobranças, limpe todos os recursos que você criou. Para excluir o cluster, use o [`az group delete`][az-group-delete] comando para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Próximas etapas

Use as [Zonas de Disponibilidade][availability-zones] para aumentar a HA com suas cargas de trabalho de cluster do AKS.

Configure o cluster para [limitar o tráfego de saída](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-update]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
