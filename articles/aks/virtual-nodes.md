---
title: Usar nós virtuais
titleSuffix: Azure Kubernetes Service
description: Visão geral de como usar o nó virtual com os serviços Kubernetess do Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634440"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Criar e configurar um cluster AKS (serviços Kubernetess do Azure) para usar nós virtuais

Para dimensionar rapidamente cargas de trabalho de aplicativos em um cluster AKS, você pode usar nós virtuais. Com nós virtuais, você tem provisionamento rápido de pods e somente paga por segundo pelo tempo de execução. Não é necessário aguardar o dimensionador automático de cluster do Kubernetes implantar nós de computação de VM para executar os pods adicionais. Os nós virtuais só têm suporte com os pods e nós do Linux.

O complemento de nós virtuais para AKS, é baseado no [Kubelet virtual][virtual-kubelet-repo]do projeto de software livre.

Este artigo fornece uma visão geral dos requisitos de rede e disponibilidade da região para usar nós virtuais, bem como as limitações conhecidas.

## <a name="regional-availability"></a>Disponibilidade regional

Todas as regiões, em que ACI dá suporte a SKUs de VNET, têm suporte para implantações de nós virtuais.

Para SKUs de CPU e memória disponíveis em cada região, verifique as [instâncias de contêiner do Azure disponibilidade de recursos para instâncias de contêiner do Azure em regiões do Azure-grupos de contêineres do Linux](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Requisitos de rede

Os nós virtuais permitem a comunicação de rede entre pods executados nas Instâncias de Contêiner do Azure (ACI) e o cluster do AKS. Para fornecer essa comunicação, uma sub-rede de rede virtual é criada e permissões delegadas são atribuídas. Os nós virtuais funcionam apenas com clusters AKS criados usando a rede *avançada* (Azure CNI). Por padrão, os clusters AKS são criados com a rede *básica* (kubenet).

Os pods em execução nas ACI (instâncias de contêiner do Azure) precisam acessar o ponto de extremidade do servidor de API do AKS para configurar a rede.

## <a name="known-limitations"></a>Limitações conhecidas

A funcionalidade de Nós Virtuais é altamente dependente do conjunto de recursos de ACI. Além das [cotas e dos limites das instâncias de contêiner do Azure](../container-instances/container-instances-quotas.md), os seguintes cenários ainda não têm suporte com nós virtuais:

* Usando a entidade de serviço para efetuar pull de imagens do ACR. A [solução alternativa](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) é usar [segredos do Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de Rede Virtual](../container-instances/container-instances-vnet.md) incluindo o emparelhamento de redes virtuais, políticas de rede do Kubernetes e o tráfego de saída para a Internet com grupos de segurança de rede.
* Contêineres de inicialização
* [Alias de host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para exec em ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não implantará pods nos nós virtuais
* Os nós virtuais dão suporte ao agendamento de pods do Linux. Você pode instalar manualmente o provedor de [ACI de Kubelet Virtual](https://github.com/virtual-kubelet/azure-aci) para agendar contêineres do Windows Server para ACI.
* Nós virtuais exigem clusters AKS com a rede CNI do Azure.
* Usando intervalos de IP autorizados do servidor de API para AKS.
* O compartilhamento de arquivos do Azure de montagem de volume oferece suporte [a uso geral v1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Siga as instruções para montar [um volume com o compartilhamento de arquivos do Azure](azure-files-volume.md)
* Não há suporte para o uso de IPv6.

## <a name="next-steps"></a>Próximas etapas

Configure os nós virtuais para seus clusters:

- [Criar nós virtuais usando CLI do Azure](virtual-nodes-cli.md)
- [Criar nós virtuais usando o portal no AKS (Serviços de Kubernetes do Azure)](virtual-nodes-portal.md)

Nós virtuais geralmente são um componente de uma solução de dimensionamento no AKS. Para saber mais sobre essas soluções de dimensionamento, consulte os artigos a seguir:

- [Uso do dimensionamento automático de pod horizontal do Kubernetes][aks-hpa]
- [Uso do dimensionador automático de cluster do Kubernetes][aks-cluster-autoscaler]
- [Veja o exemplo de dimensionamento automático para Nós Virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca de software livre do Kubelet virtual][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
