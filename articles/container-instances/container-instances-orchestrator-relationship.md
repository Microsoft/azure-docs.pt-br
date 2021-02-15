---
title: Instâncias de contêiner e orquestração de contêiner
description: Entender como as Instâncias de Contêiner do Azure interagem com orquestradores de contêiner.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148602"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instâncias de Contêiner do Azure e orquestradores de contêiner

Devido ao seu tamanho reduzido e a orientação a aplicativos, os contêineres são adequados para ambientes de entrega com agilidade e arquiteturas baseadas em microsserviço. A tarefa de automatizar e gerenciar um grande número de contêineres e como eles interagem é conhecida como *orquestração*. Orchestrators de contêiner populares incluem Kubernetes, DC/OS e Docker Swarm.

As Instâncias de Contêiner do Azure fornecem alguns dos recursos básicos de agendamento de plataformas de orquestração. E quando não abrangem os serviços de maior valor que essas plataformas fornecem, as Instâncias de Contêiner do Azure podem ser complementares a elas. Este artigo descreve o escopo do que é controlado pelas Instâncias de Contêiner do Azure e como os orquestradores de contêiner completos podem interagir com elas.

## <a name="traditional-orchestration"></a>Orquestração tradicional

A definição padrão de orquestração inclui as seguintes tarefas:

- **Agendamento**: dada uma imagem de contêiner e uma solicitação de recurso, localizar um computador adequado no qual executar o contêiner.
- **Afinidade/Antiafinidade**: especificar que um conjunto de contêineres devem ser executados próximo uns aos outros (para desempenho) ou suficientemente distantes (para disponibilidade).
- **Monitoramento de integridade**: inspecionar falhas de contêiner e automaticamente reagendá-los.
- **Failover**: controlar o que está em execução em cada computador e reagendar contêineres de computadores com falha para nós íntegros.
- **Dimensionamento**: adicionar ou remover instâncias de contêiner de acordo com a demanda, manualmente ou automaticamente.
- **Rede**: fornecer uma rede de sobreposição a fim de coordenar contêineres para que se comuniquem entre vários computadores host.
- **Descoberta de serviço**: habilitar contêineres para localizar uns aos outros automaticamente, mesmo que eles se movam entre computadores host e alterem os endereços IP.
- **Atualizações de aplicativo coordenadas**: gerenciar atualizações de contêiner para evitar tempo de inatividade de aplicativo e habilitar a reversão se algo der errado.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orquestração com Instâncias de Contêiner do Azure: uma abordagem em camadas

As Instâncias de Contêiner do Azure permitem uma abordagem em camadas à orquestração, fornecendo todos os recursos de agendamento e gerenciamento necessários para executar um único contêiner, permitindo ao mesmo tempo que as plataformas de orquestração gerenciem tarefas de vários contêineres sobre elas.

Como a infra-estrutura subjacente das instâncias de contêiner é gerenciada pelo Azure, uma plataforma de orquestração não precisa se preocupar em localizar um computador host apropriado no qual executar um único contêiner. A elasticidade da nuvem garante que um esteja sempre disponível. Em vez disso, o orquestrador pode se concentrar nas tarefas que simplificam o desenvolvimento de arquiteturas de vários contêineres, incluindo o dimensionamento e as atualizações coordenadas.

## <a name="scenarios"></a>Cenários

Embora a integração do orquestrador com as Instâncias de Contêiner do Azure ainda seja recente, estimamos que alguns ambientes diferentes podem surgir:

### <a name="orchestration-of-container-instances-exclusively"></a>Orquestração de instâncias de contêiner de maneira exclusiva

Como elas são iniciadas rapidamente e são cobradas pelo segundo, um ambiente baseado exclusivamente em Instâncias de Contêiner do Azure oferece a maneira mais rápida para começar e para lidar com cargas de trabalho altamente variáveis.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinação de instâncias de contêiner e contêineres em Máquinas Virtuais

Para cargas de trabalho estáveis de longa execução, orquestrar contêineres em um cluster de máquinas virtuais dedicadas geralmente é mais barato do que executar os mesmos contêineres com Instâncias de Contêiner do Azure. No entanto, as instâncias de contêiner oferecem uma ótima solução para expandir e reduzir rapidamente a capacidade total para lidar com picos de curta duração ou inesperados no uso.

Em vez de expandir o número de máquinas virtuais em seu cluster e, em seguida, implantar contêineres adicionais nessas máquinas, o orquestrador pode simplesmente agendar os contêineres adicionais nas Instâncias de Contêiner do Azure e excluí-los quando não forem mais necessários.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Implementação de exemplo: nós virtuais para o Serviço de Kubernetes do Azure (AKS)

Para dimensionar rapidamente as cargas de trabalho do aplicativo em um cluster do [Serviço de Kubernetes do Azure](../aks/intro-kubernetes.md) (AKS), você pode usar os *nós virtuais* criados dinamicamente nas Instâncias de Contêiner do Azure. Nós virtuais permitem a comunicação de rede entre pods executados em ACI e o cluster do AKS. 

Os nós virtuais atualmente dão suporte a instâncias de contêiner do Linux. Comece com os nós virtuais usando a [CLI do Azure](../aks/virtual-nodes-cli.md) ou o [portal do Azure](../aks/virtual-nodes-portal.md).

Nós virtuais usam software livre [Virtual Kubelet][aci-connector-k8s] para imitar o Kubernetes [kubelet][kubelet-doc] registrando-se como um nó com capacidade ilimitada. O Virtual Kubelet expede a criação de [pods][pod-doc] como grupos de contêineres em Instâncias de Contêiner do Azure.

Consulte o projeto [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) para obter exemplos adicionais de estender a API do Kubernetes em plataformas de contêiner sem servidor.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro contêiner com as Instâncias de Contêiner do Azure usando o [guia de início rápido](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/