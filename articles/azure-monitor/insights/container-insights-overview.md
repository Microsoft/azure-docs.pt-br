---
title: Visão geral do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve o Azure Monitor para contêineres que monitora as soluções de Insights do Contêiner AKS e o valor que ele oferece monitorando a integridade dos clusters AKS e de Instâncias de Contêiner no Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 96a312630c92048f36f79e3bec18f83ed5a445ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414141"
---
# <a name="azure-monitor-for-containers-overview"></a>Visão geral do Azure Monitor para contêineres

O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em Instâncias de Contêiner do Azure ou em clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure). Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Os logs do contêiner também são coletados.  Depois de habilitar o monitoramento de clusters do kubernetes, as métricas e os logs são coletados automaticamente para você por meio de uma versão em contêiner do agente de Log Analytics para Linux. As métricas são gravadas no repositório de métricas e os dados de log são gravados no repositório de logs associado ao seu espaço de trabalho [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor para arquitetura de contêineres](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que o Azure Monitor para contêineres fornece?

O Azure Monitor para contêineres fornece uma experiência de monitoramento abrangente usando diferentes recursos do Azure Monitor, permitindo que você entenda o desempenho e a integridade do cluster kubernetes e das cargas de trabalho do contêiner. Com Azure Monitor para contêineres, você pode:

* Identificar contêineres AKS que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identifique a utilização de processador e de memória de grupos de contêineres e seus contêineres hospedados em Instâncias de Contêiner do Azure.  
* Identificar em que local o contêiner reside em um controlador ou em um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod.
* Examine a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod.
* Compreender o comportamento do cluster sob cargas mais pesadas e médias. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar. 
* Configure alertas para notificá-lo proativamente ou registre-o quando a utilização de CPU e memória em nós ou contêineres exceder seus limites.
* Integre com o [Prometheus](https://prometheus.io/docs/introduction/overview/) para exibir as métricas de aplicativo e carga de trabalho coletadas de nós e kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, painéis e executar análise detalhada.

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

Confira o vídeo a seguir fornecendo um aprofundamento no nível intermediário para ajudá-lo a saber mais sobre como monitorar o cluster AKS com Azure Monitor para contêineres.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como acessar esse recurso?

Acesse o Azure Monitor para contêineres de duas maneiras, pelo Azure Monitor ou diretamente no cluster AKS selecionado. Em Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, que são monitorados e que não estão, permitindo Pesquisar e filtrar entre suas assinaturas e grupos de recursos e, em seguida, analisar Azure Monitor para contêineres do contêiner selecionado.  Caso contrário, você pode acessar o recurso diretamente de um contêiner AKS selecionado na página AKS.  

![Visão geral dos métodos para acessar o Azure Monitor para contêineres](./media/container-insights-overview/azmon-containers-experience.png)

Se você estiver interessado em monitorar e gerenciar seus hosts de contêiner do Docker e do Windows em execução fora do AKS para exibir a configuração, a auditoria e a utilização de recursos, consulte a [solução de monitoramento de contêiner](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
