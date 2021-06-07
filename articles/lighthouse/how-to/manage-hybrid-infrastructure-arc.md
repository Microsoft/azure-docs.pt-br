---
title: Gerenciar a infraestrutura híbrida em escala com o arco do Azure
description: Saiba como gerenciar efetivamente os computadores de seus clientes e clusters kubernetes fora do Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: da7f016b507f8f876d27f6924f87f078a7806563
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563592"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Gerenciar a infraestrutura híbrida em escala com o arco do Azure

Como um provedor de serviços, você pode ter integrado vários locatários de clientes ao [Azure Lighthouse](../overview.md). O Azure Lighthouse permite que os provedores de serviços executem operações em escala em vários locatários Azure Active Directory (Azure AD) de uma vez, tornando as tarefas de gerenciamento mais eficientes.

O [Arc do Azure](../../azure-arc/overview.md) ajuda a simplificar ambientes distribuídos e complexos entre locais, Edge e multinuvem, permitindo a implantação de serviços do Azure em qualquer lugar e estendendo o gerenciamento do Azure para qualquer infraestrutura.

Com os [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md), os clientes podem gerenciar qualquer computador Windows e Linux hospedado fora do Azure em sua rede corporativa, da mesma maneira que gerenciam máquinas virtuais nativas do Azure. Ao vincular um computador híbrido ao Azure, ele se torna conectado e é tratado como um recurso no Azure. Os provedores de serviços podem gerenciar esses computadores não Azure junto com os recursos do Azure dos seus clientes.

O [kubernetes habilitado para Arc do Azure](../../azure-arc/kubernetes/overview.md) permite que os clientes anexem e configurem clusters do kubernetes dentro ou fora do Azure. Quando um cluster kubernetes é anexado ao arco do Azure, ele aparecerá na portal do Azure, com uma ID de Azure Resource Manager e uma identidade gerenciada. Os clusters são anexados a assinaturas do Azure padrão, estão localizados em um grupo de recursos e podem receber marcas, assim como qualquer outro recurso do Azure.

Este tópico fornece uma visão geral de como os provedores de serviços podem usar os servidores habilitados para Arc do Azure e o Arc do Azure habilitado kubernetes de forma escalonável para gerenciar o ambiente híbrido de seus clientes, com visibilidade em todos os locatários de clientes gerenciados.

> [!TIP]
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, essa orientação também se aplica a [empresas que usam o Azure Lighthouse para gerenciar vários locatários](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Gerenciar servidores híbridos em escala com os servidores habilitados para Arc do Azure

Como um provedor de serviços, você pode gerenciar computadores Windows Server ou Linux locais fora do Azure que seus clientes se conectaram à sua assinatura usando o [agente de máquina conectado do Azure](../../azure-arc/servers/agent-overview.md).

Ao exibir recursos para uma assinatura delegada no portal do Azure, você verá esses computadores conectados rotulados com o **arco do Azure**. Você pode gerenciar esses computadores conectados usando construções do Azure, como Azure Policy e marcação, da mesma maneira que você gerenciaria os recursos do Azure do cliente. Você também pode trabalhar entre locatários do cliente para gerenciar todos os computadores híbridos conectados.

Por exemplo, você pode [garantir que o mesmo conjunto de políticas seja aplicado nos computadores híbridos dos clientes](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Você também pode usar a central de segurança do Azure para monitorar a conformidade em todos os ambientes híbridos de seus clientes ou [usar Azure monitor para coletar dados diretamente de seus computadores híbridos](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) para um espaço de trabalho log Analytics. [As extensões de máquina virtual](../../azure-arc/servers/manage-vm-extensions.md) podem ser implantadas em VMs não Azure Windows e Linux, simplificando o gerenciamento de máquinas híbridas do cliente.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Gerenciar clusters kubernetes híbridos em escala com o kubernetes habilitado para Arc do Azure

Você pode gerenciar clusters kubernetes que foram [conectados à assinatura de um cliente com o Arc do Azure](../../azure-arc/kubernetes/quickstart-connect-cluster.md), assim como se estivessem em execução no Azure.

Se o cliente tiver criado uma [conta de entidade de serviço para carregar clusters kubernetes para o Arc do Azure](../../azure-arc/kubernetes/create-onboarding-service-principal.md), você poderá acessar essa conta de entidade de serviço para carregar e gerenciar clusters. Isso pode ser feito por usuários no locatário de gerenciamento que receberam a função interna "cluster kubernetes – integração de arco do Azure" do Azure quando a assinatura que contém a conta da entidade de serviço foi [integrada ao Azure Lighthouse](onboard-customer.md).

Você pode implantar [configurações](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) e [gráficos do Helm](../../azure-arc/kubernetes/use-gitops-with-helm.md) usando o GitOps para clusters conectados.

Você também pode monitorar os clusters conectados com Azure Monitor e [usar Azure Policy para aplicar configurações de cluster em escala](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Próximas etapas

- Explore os JumpStarts e exemplos no [repositório GitHub do Arc do Azure](https://github.com/microsoft/azure_arc).
- Saiba mais sobre os [cenários com suporte para servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md#supported-scenarios).
- Saiba mais sobre as [distribuições do kubernetes com suporte do Azure Arc](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Saiba como [implantar uma política em escala](policy-at-scale.md).
- Saiba como [usar os logs de Azure monitor em escala](monitor-at-scale.md).