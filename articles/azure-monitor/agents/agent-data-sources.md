---
title: Log Analytics fontes de dados do agente no Azure Monitor
description: As fontes de dados definem os dados coletados pelo Azure Monitor de agentes e outras fontes conectadas.  Este artigo descreve o conceito de como o Azure Monitor usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041920"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Log Analytics fontes de dados do agente no Azure Monitor
Os dados que Azure Monitor coleta de máquinas virtuais com o agente de [log Analytics](./log-analytics-agent.md) são definidos pelas fontes de dados que você configura no [espaço de trabalho log Analytics](../logs/data-platform-logs.md).   Cada fonte de dados cria registros de um determinado tipo com cada tipo de tendo seu próprio conjunto de propriedades.

> [!IMPORTANT]
> Este artigo aborda as fontes de dados para o [agente de log Analytics](./log-analytics-agent.md) , que é um dos agentes usados pelo Azure monitor. Outros agentes coletam dados diferentes e são configurados de forma diferente. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que eles podem coletar.

![Coleta de dados de log](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> As fontes de dados descritas neste artigo se aplicam somente a máquinas virtuais que executam o agente de Log Analytics. 

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
A tabela a seguir lista as fontes de dados do agente que estão disponíveis atualmente com o agente de Log Analytics.  Cada uma tem um link para um artigo à parte que fornece detalhes para aquela fonte de dados.   Fornece também informações sobre o método e a frequência de coleta. 


| Fonte de dados | Plataforma | Agente do log Analytics | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Logs personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Logs personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Logs do IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da configuração de rolagem de arquivo de log |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| [Logs de eventos do Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurando fontes de dados
Para configurar fontes de dados para agentes de Log Analytics, vá para o menu **log Analytics espaços de trabalho** no portal do Azure e selecione um espaço de trabalho. Clique em **configuração de agentes**. Selecione a guia da fonte de dados que você deseja configurar. Você pode seguir os links na tabela acima para a documentação de cada fonte de dados e detalhes sobre sua configuração.

Qualquer configuração é entregue a todos os agentes conectados a esse espaço de trabalho.  Você não pode excluir nenhum agente conectado dessa configuração.

[![Configurar eventos do Windows](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>Coleta de dados
As configurações de fonte de dados são entregues aos agentes que estão diretamente conectados ao Azure Monitor em alguns minutos.  Os dados especificados são coletados do agente e entregues diretamente para o Azure Monitor em intervalos específicos a cada fonte de dados.  Consulte a documentação para cada fonte de dados para encontrar essas especificações.

Para agentes do System Center Operations Manager em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada cinco minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gerenciamento que os encaminhará para o Azure Monitor ou o agente enviará os dados para o Azure Monitor sem passar pelo servidor de gerenciamento. Confira [Detalhes da coleta de dados para soluções de monitoramento no Azure](../monitor-reference.md) para obter detalhes.  Leia mais sobre os detalhes da conexão do Operations Manager e do Azure Monitor e a modificação da frequência de entrega da configuração em [Configurar a integração com o System Center Operations Manager](./om-agents.md).

Se o agente não puder se conectar ao Azure Monitor nem ao Operations Manager, ele continuará coletando dados que serão fornecidos quando ele estabelecer uma conexão.  Dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo do cache do cliente, ou se o agente não for capaz de estabelecer uma conexão dentro de 24 horas.

## <a name="log-records"></a>Registros de log
Todos os dados de log coletados pelo Azure Monitor são armazenados no workspace como registros.  Registros coletados por diferentes fontes de dados terão seu próprio conjunto de propriedades e serão identificados por sua propriedade **Type** .  Consulte a documentação para cada fonte de dados e a solução para obter detalhes sobre cada tipo de registro.

## <a name="next-steps"></a>Próximas etapas
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidades ao Azure Monitor e também coletam dados no workspace.
* Saiba mais sobre as [consultas de log](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções de monitoramento.  
* Configure [alertas](../alerts/alerts-overview.md) para notificá-lo de forma proativa sobre dados críticos coletados de fontes de dados e soluções de monitoramento.