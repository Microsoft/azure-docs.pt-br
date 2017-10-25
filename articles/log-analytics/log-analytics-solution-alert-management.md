---
title: "Solução Gerenciamento de Alertas no OMS (Operations Management Suite) | Microsoft Docs"
description: "A solução de Gerenciamento de Alertas no Log Analytics ajuda a analisar todos os alertas em seu ambiente.  Além de consolidar alertas gerados no OMS, ela importa alertas dos grupos de gerenciamento conectados do System Center Operations Manager para o Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: bwren
ms.openlocfilehash: 4a394ead09794c52e92dbd2db8be8166d1fab83b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Solução Gerenciamento de Alertas no OMS (Operations Management Suite)

![Ícone do Gerenciamento de Alertas](media/log-analytics-solution-alert-management/icon.png)

A solução de Gerenciamento de Alertas ajuda a analisar todos os alertas em seu repositório do Log Analytics.  Esses alertas podem ser provenientes de várias origens, incluindo aquelas [criadas pelo Log Analytics](log-analytics-alerts.md) ou [importadas do Nagios ou do Zabbix](log-analytics-linux-agents.md).  A solução também importa alertas de quaisquer [grupos de gerenciamento do System Center Operations Manager conectados](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Pré-requisitos
A solução funcionará com quaisquer registros no repositório do Log Analytics que sejam do tipo **Alerta**, portanto, você deverá executar qualquer configuração que for necessária para coletar esses registros.

- Para alertas do Log Analytics, [crie regras de alerta](log-analytics-alerts.md) para criar registros de alerta diretamente no repositório.
- Para alertas do Nagios e Zabbix, [configure esses servidores](log-analytics-linux-agents.md) para enviar alertas para o Log Analytics.
- Para alertas do System Center Operations Manager, [conecte seu grupo de gerenciamento do Operations Manager para seu espaço de trabalho do Log Analytics](log-analytics-om-agents.md).  Todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.  

## <a name="configuration"></a>Configuração
Adicione a solução de Gerenciamento de Alertas ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções](log-analytics-add-solutions.md).  Não é necessária nenhuma configuração.

## <a name="management-packs"></a>Pacotes de gerenciamento
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado ao seu espaço de trabalho do OMS, os pacotes de gerenciamento a seguir serão instalados no System Center Operations Manager quando você adicionar essa solução.  Não é necessária nenhuma configuração nem a manutenção dos pacotes de gerenciamento.  

* Gerenciamento de Alertas do Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).

## <a name="data-collection"></a>Coleta de dados
### <a name="agents"></a>Agentes
A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não |Agentes diretos do Windows não geram alertas.  Alertas do Log Analytics podem ser criados de eventos e dados de desempenho coletados de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não |Agentes diretos do Linux não geram alertas.  Alertas do Log Analytics podem ser criados de eventos e dados de desempenho coletados de agentes do Linux.  Alertas do Nagios e Zabbix são coletados desses servidores que exigem o agente do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](log-analytics-om-agents.md) |Sim |Os alertas gerados em agentes do System Center Operations Manager são entregues ao grupo de gerenciamento e, em seguida, encaminhados para o Log Analytics.<br><br>Uma conexão direta de agentes do Operations Manager ao Log Analytics não é necessária. Os dados de alerta são encaminhados do grupo de gerenciamento para o repositório do Log Analytics. |


### <a name="collection-frequency"></a>Frequência de coleta
- Registros de alerta ficam disponíveis para a solução assim que eles são armazenados no repositório.
- Os dados de alerta são enviados do grupo de gerenciamento do System Center Operations Manager para o Log Analytics a cada três minutos.  

## <a name="using-the-solution"></a>Usando a solução
Ao adicionar a solução de Gerenciamento de Alertas ao seu espaço de trabalho do OMS, o bloco **Gerenciamento de Alertas** é adicionado ao painel do OMS.  Esse bloco exibe uma contagem e representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não é possível alterar esse intervalo de tempo.

![Bloco do Gerenciamento de Alertas](media/log-analytics-solution-alert-management/tile.png)

Clique no bloco **Gerenciamento de Alertas** para abrir o painel **Gerenciamento de Alertas**.  O painel inclui as colunas na tabela a seguir.  Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e intervalo de tempo especificados.  É possível executar uma pesquisa de log que fornece a lista inteira clicando em **Ver todos** na parte inferior da coluna ou clicando no cabeçalho de coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas críticos |Todos os alertas com uma severidade de Crítico agrupados por nome do alerta.  Clique em um nome de alerta para executar uma pesquisa de log retornando todos os registros desse alerta. |
| Alertas de aviso |Todos os alertas com uma severidade de Aviso agrupados por nome do alerta.  Clique em um nome de alerta para executar uma pesquisa de log retornando todos os registros desse alerta. |
| Alertas ativos do SCOM |Todos os alertas coletados do Operations Manager com qualquer estado diferente de *Fechado*, agrupados pela origem que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer severidade agrupados por nome do alerta. Inclui somente alertas do Operations Manager com qualquer estado diferente de *Fechado*. |

Se você rolar para a direita, o painel listará várias consultas comuns nas quais você poderá clicar para realizar uma [pesquisa de logs](log-analytics-log-searches.md) e obter dados de alerta.

![Painel do Gerenciamento de Alertas](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Registros do Log Analytics
A solução de Gerenciamento de Alertas analisa qualquer registro com um tipo de **Alerta**.  Alertas criados pelo Log Analytics ou coletados do Nagios ou Zabbix não são coletados diretamente pela solução.

A solução importa alertas do System Center Operations Manager e cria um registro correspondente para cada um com um tipo de **Alerta** e um SourceSystem de **OpsManager**.  Esses registros têm as propriedades descritas na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OpsManager* |
| AlertContext |Detalhes do item de dados que fez com que o alerta fosse gerado em formato XML. |
| AlertDescription |Descrição detalhada do alerta. |
| AlertId |GUID do alerta. |
| AlertName |Nome do alerta. |
| AlertPriority |Nível de prioridade do alerta. |
| AlertSeverity |Nível de severidade do alerta. |
| AlertState |Último estado de resolução do alerta. |
| LastModifiedBy |Nome do usuário que modificou o alerta pela última vez. |
| ManagementGroupName |Nome do grupo de gerenciamento no qual o alerta foi gerado. |
| RepeatCount |Número de vezes que o mesmo alerta foi gerado para o mesmo objeto monitorado desde sua resolução. |
| ResolvedBy |Nome do usuário que resolveu o alerta. Vazio, se o alerta ainda não foi resolvido. |
| SourceDisplayName |Nome de exibição do objeto de monitoramento que gerou o alerta. |
| SourceFullName |Nome completo do objeto de monitoramento que gerou o alerta. |
| TicketId |ID do Tíquete do alerta, se o ambiente do System Center Operations Manager é integrado com um processo para atribuição de tíquetes para alertas.  Vazio, se nenhuma ID do Tíquete for atribuída. |
| TimeGenerated |Data e hora em que o alerta foi criado. |
| TimeLastModified |Data e hora em que o alerta foi alterado pela última vez. |
| TimeRaised |Data e hora em que o alerta foi gerado. |
| TimeResolved |Data e hora em que o alerta foi resolvido. Vazio, se o alerta ainda não foi resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros de alerta coletados por essa solução: 

| Consultar | Descrição |
|:--- |:--- |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR |Alertas críticos gerados nas últimas 24 horas |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR |Alertas de aviso gerados nas últimas 24 horas |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName |Fontes com alertas ativos gerados nas últimas 24 horas |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed |Alertas críticos gerados nas últimas 24 horas que ainda estão ativos |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed |Alertas gerados nas últimas 24 horas que agora estão fechados |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity |Alertas gerados durante o último dia agrupados por severidade |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc |Alertas gerados durante o último dia classificados por valor de contagem de repetição |


>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir:
>
>| Consultar | Descrição |
|:---|:---|
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) |Alertas críticos gerados nas últimas 24 horas |
| Alert &#124; where AlertSeverity == "warning" and TimeRaised > ago(24h) |Alertas de aviso gerados nas últimas 24 horas |
| Alert &#124; where SourceSystem == "OpsManager" and AlertState != "Closed" and TimeRaised > ago(24h) &#124; summarize Count = count() by SourceDisplayName |Fontes com alertas ativos gerados nas últimas 24 horas |
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) and AlertState != "Closed" |Alertas críticos gerados nas últimas 24 horas que ainda estão ativos |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(24h) and AlertState == "Closed" |Alertas gerados nas últimas 24 horas que agora estão fechados |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; summarize Count = count() by AlertSeverity |Alertas gerados durante o último dia agrupados por severidade |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; sort by RepeatCount desc |Alertas gerados durante o último dia classificados por valor de contagem de repetição |


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [Alertas no Log Analytics](log-analytics-alerts.md) para obter detalhes sobre como gerar alertas por meio do Log Analytics.
