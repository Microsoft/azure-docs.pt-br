---
title: "Instâncias de Contêiner do Azure e Orquestração de contêiner"
description: "Entender como as Instâncias de Contêiner do Azure interagem com orquestradores de contêiner"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b960d56188abf102cf4a8894582203205eaa9a03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instâncias de Contêiner do Azure e orquestradores de contêiner

Devido ao seu tamanho reduzido e a orientação a aplicativos, os contêineres são adequados para ambientes de entrega com agilidade e arquiteturas baseadas em microsserviço. A tarefa de automatizar e gerenciar um grande número de contêineres e como eles interagem é conhecida como *orquestração*. Orquestradores de contêiner populares incluem Kubernetes, DC/SO e Docker Swarm, que estão disponíveis no [Serviço de Contêiner do Azure](https://docs.microsoft.com/azure/container-service/).

As Instâncias de Contêiner do Azure fornecem alguns dos recursos básicos de agendamento das plataformas de orquestração, mas elas não abrangem os serviços de maior valor que essas plataformas fornecem e podem realmente ser complementares com elas. Este artigo descreve o escopo do que é controlado pelas Instâncias de Contêiner do Azure e como os orquestradores de contêiner completos podem interagir com elas.

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

Como toda a infra-estrutura subjacente das Instâncias de Contêiner é gerenciada pelo Azure, uma plataforma de orquestração não precisa se preocupar em localizar um computador host apropriado no qual executar um único contêiner. A elasticidade da nuvem garante que um esteja sempre disponível. Em vez disso, o orquestrador pode se concentrar nas tarefas que simplificam o desenvolvimento de arquiteturas de vários contêineres, incluindo o dimensionamento e as atualizações coordenadas.



## <a name="potential-scenarios"></a>Cenários possíveis

Embora a integração do orquestrador com as Instâncias de Contêiner do Azure ainda seja recente, estimamos que alguns ambientes diferentes podem surgir:

### <a name="orchestration-of-container-instances-exclusively"></a>Orquestração de instâncias de contêiner de maneira exclusiva

Como elas são iniciadas rapidamente e são cobradas pelo segundo, um ambiente baseado exclusivamente em Instâncias de Contêiner do Azure oferece a maneira mais rápida para começar e para lidar com cargas de trabalho altamente variáveis.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinação de Instâncias de Contêiner e Contêineres em Máquinas Virtuais

Para cargas de trabalho estáveis de longa execução, orquestrar contêineres em um cluster de máquinas virtuais dedicadas geralmente será mais barato do que executar os mesmos contêineres com Instâncias de Contêiner. No entanto, as Instâncias de Contêiner oferecem uma ótima solução para expandir e reduzir rapidamente a capacidade total para lidar com picos de curta duração ou inesperados no uso. Em vez de expandir o número de máquinas virtuais em seu cluster e, em seguida, implantar contêineres adicionais nessas máquinas, o orquestrador pode simplesmente agendar os contêineres adicionais usando as Instâncias de Contêiner e excluí-los quando não forem mais necessários.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Exemplo de implementação: conector de Instâncias de Contêiner do Azure para Kubernetes

Para demonstrar como as plataformas de orquestração de contêiner podem se integrar com as Instâncias de Contêiner do Azure, começamos a criar um [conector de exemplo para Kubernetes][aci-connector-k8s].

O conector para Kubernetes imita a [kubelet][kubelet-doc] ao registrar-se como um nó com capacidade ilimitada e expedir a criação de [pods][pod-doc] como grupos de contêineres nas Instâncias de Contêiner do Azure.

<!-- ![ACI Connector for Kubernetes][aci-connector-k8s-gif] -->

Os conectores para outros orquestradores podem ser criados da mesma forma, integrados com primitivos de plataforma para combinar a potência da API de orquestrador com a velocidade e a simplicidade do gerenciamento de contêineres nas Instâncias de Contêiner do Azure.

> [!WARNING]
> O conector ACI para Kubernetes é *experimental* e não deve ser usado em produção.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro contêiner com as Instâncias de Contêiner do Azure usando o [guia de início rápido](container-instances-quickstart.md).

<!-- IMAGES -->
[aci-connector-k8s-gif]: ./media/container-instances-orchestrator-relationship/aci-connector-k8s.gif

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/azure/aci-connector-k8s
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/