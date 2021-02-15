---
title: Análise de dependência na avaliação do servidor de migrações para Azure
description: Descreve como usar a análise de dependência para avaliação usando a avaliação de servidor de migrações para Azure.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: 378a85ed77a6eedeb928dee541046db1909da553
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491984"
---
# <a name="dependency-analysis"></a>dependency analysis

Este artigo descreve a análise de dependência em migrações para Azure: avaliação de servidor.


A análise de dependência identifica as dependências entre as máquinas locais descobertas. Ele fornece essas vantagens: 

- Você pode reunir computadores em grupos para avaliação, com mais precisão, com maior confiança.
- Você pode identificar as máquinas que devem ser migradas juntas. Isso é especialmente útil se você não tiver certeza de quais computadores fazem parte de uma implantação de aplicativo que você deseja migrar para o Azure.
- Você pode identificar se os computadores estão em uso e quais computadores podem ser descomissionados em vez de migrados.
- A análise de dependências ajuda a garantir que nada seja deixado para trás e, portanto, evita interrupções surpresa após a migração.
- [Examine](common-questions-discovery-assessment.md#what-is-dependency-visualization) as perguntas comuns sobre a análise de dependência.


## <a name="analysis-types"></a>Tipos de análise

Há duas opções para implantar a análise de dependência

**Opção** | **Detalhes** | **Nuvem pública** | **Azure Governamental**
----  |---- | ---- 
**Sem agente** | Sonda dados de VMs VMware usando APIs vSphere.<br/><br/> Você não precisa instalar agentes em VMs.<br/><br/> Esta opção está atualmente em visualização, somente para VMs VMware. | Com suporte. | Com suporte.
**Análise baseada em agente** | Usa a [solução mapa do serviço](../azure-monitor/insights/service-map.md) no Azure monitor para habilitar a visualização e a análise de dependência.<br/><br/> Você precisa instalar agentes em cada computador local que deseja analisar. | Compatível | Não há suporte.


## <a name="agentless-analysis"></a>Análise sem agente

A análise de dependência sem agente funciona capturando dados de conexão TCP de computadores para os quais ele está habilitado. Nenhum agente é instalado em VMs. As conexões com o mesmo servidor de origem e processo, e o servidor de destino, o processo e a porta são agrupadas logicamente em uma dependência. Você pode visualizar dados de dependência capturados em uma exibição de mapa ou exportá-los como um CSV. Nenhum agente está instalado nos computadores que você deseja analisar.

### <a name="dependency-data"></a>Dados de dependência

Após o início da descoberta de dados de dependência, a sondagem começa:

- O dispositivo de migrações para Azure pesquisa dados de conexão TCP de computadores a cada cinco minutos para coletar dados.
- Os dados são coletados de VMs convidadas por meio de vCenter Server, usando APIs vSphere.
- A sondagem coleta esses dados:

    - Nome dos processos que têm conexões ativas.
    - Nome do aplicativo que executa processos que têm conexões ativas.
    - Porta de destino nas conexões ativas.

- Os dados coletados são processados no dispositivo de migrações para Azure, para deduzir informações de identidade e enviados para migrações para Azure a cada seis horas


## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análise baseada em agente, a avaliação do servidor usa a solução [mapa do serviço](../azure-monitor/insights/service-map.md) no Azure monitor. Instale o [agente Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)em cada computador que você deseja analisar.

### <a name="dependency-data"></a>Dados de dependência

A análise baseada em agente fornece esses dados:

- Nome do servidor do computador de origem, processo, nome do aplicativo.
- Nome do servidor do computador de destino, processo, nome do aplicativo e porta.
- Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 



## <a name="compare-agentless-and-agent-based"></a>Compare sem agente e com base em agente

As diferenças entre a visualização sem agente e a visualização baseada em agente são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Suporte** | Somente em visualização para VMs VMware. [Examine](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operacionais com suporte. | Em disponibilidade geral (GA).
**Agente** | Nenhum agente é necessário nos computadores que você deseja analisar. | Agentes necessários em cada computador local que você deseja analisar.
**Log Analytics** | Não necessário. | As migrações para Azure usam a solução [mapa do serviço](../azure-monitor/insights/service-map.md) em [logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md) para análise de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics a um projeto de migrações para Azure. O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).
**Processo** | Captura dados de conexão TCP. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Mapa do Serviço agentes instalados em um computador coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
**Dados** | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta. | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta.<br/><br/> Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 
**Visualização** | O mapa de dependências de um único servidor pode ser exibido durante uma duração de uma hora a 30 dias. | Mapa de dependências de um único servidor.<br/><br/> Mapa de dependências de um grupo de servidores.<br/><br/>  O mapa pode ser exibido somente em uma hora.<br/><br/> Adicionar e remover servidores de um grupo da exibição de mapa.
Exportação de dados | Os últimos 30 dias de dados podem ser baixados em um formato CSV. | Os dados podem ser consultados com Log Analytics.



## <a name="next-steps"></a>Próximas etapas

- [Configure](how-to-create-group-machine-dependencies.md) a visualização de dependência baseada em agente.
- [Experimente](how-to-create-group-machine-dependencies-agentless.md) a visualização de dependência sem agente para VMs VMware.
- Examine as [perguntas comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre a visualização de dependência.
