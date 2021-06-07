---
title: Coletar logs do IIS com Log Analytics Agent no Azure Monitor
description: O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Azure Monitor.  Este artigo descreve como configurar a coleta de logs do IIS e os detalhes dos registros que eles criam no repositório do Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 87cb19daa23c9fcca601771a9fe168b98be02627
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586260"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Coletar logs do IIS com Log Analytics Agent no Azure Monitor
O Serviços de Informações da Internet (IIS) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo agente de Log Analytics e armazenados em [logs de Azure monitor](../data-platform.md).

> [!IMPORTANT]
> Este artigo aborda a coleta de logs do IIS com o [agente de log Analytics](./log-analytics-agent.md) , que é um dos agentes usados pelo Azure monitor. Outros agentes coletam dados diferentes e são configurados de forma diferente. Consulte [visão geral dos agentes de Azure monitor](../agents/agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que eles podem coletar.

![Logs IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurando logs do IIS
O Azure Monitor coleta entradas de arquivos de log criados pelo IIS, por isso você deve [configurar o IIS para o registro em log](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

O Azure Monitor só oferece suporte a arquivos de log do IIS armazenados em formato W3C e não oferece suporte a campos personalizados ou a registro em Log Avançado do IIS. Ele não coleta logs no formato nativo IIS ou NCSA.

Configure os logs do IIS no Azure Monitor no [menu de configuração do agente](../agents/agent-data-sources.md#configuring-data-sources) para o agente de log Analytics.  Não há nenhuma outra configuração necessária além da seleção de **Collect W3C format IIS log files**(Coletar arquivos de log do IIS no formato W3C).


## <a name="data-collection"></a>Coleta de dados
O Azure Monitor coleta entradas de log do IIS de cada agente sempre que o carimbo de data/hora do log é alterado. O log é lido a cada **5 minutos**. Se, por algum motivo, o IIS não atualizar o carimbo de data/hora antes do horário de substituição quando um novo arquivo for criado, as entradas serão coletadas após a criação do novo arquivo. A frequência da criação do novo arquivo é controlada pela configuração **agenda de substituição do arquivo de log** para o site do IIS, que é uma vez por dia por padrão. Se a configuração for por **hora**, Azure monitor coletará o log a cada hora. Se a configuração for **diária**, Azure monitor coletará o log a cada 24 horas.

> [!IMPORTANT]
> É recomendável definir a **agenda de substituição do arquivo de log** para a **cada hora**. Se ele estiver definido como **diário**, você poderá enfrentar picos nos seus dados, pois eles só serão coletados uma vez por dia.

## <a name="iis-log-record-properties"></a>Propriedades de registro de log do IIS
Os registros log do IIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador do qual o evento foi coletado. |
| cIP |Endereço IP do cliente. |
| csMethod |Método de solicitação, como GET ou POST. |
| csReferer |Site do qual o usuário seguiu um link para o site atual. |
| csUserAgent |O tipo de navegador do cliente. |
| csUserName |Nome do usuário autenticado que acessou o servidor. Os usuários anônimos são indicados por um hífen. |
| csUriStem |Destino da solicitação, como uma página da Web. |
| csUriQuery |Consulta, se houver, que o cliente estava tentando executar. |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes do Operations Manager.  Para outros agentes, ele é AOI-\<workspace ID\> |
| RemoteIPCountry |País/região do endereço IP do cliente. |
| RemoteIPLatitude |Latitude do endereço IP do cliente. |
| RemoteIPLongitude |Longitude do endereço IP do cliente. |
| scStatus |Código de status HTTP. |
| scSubStatus |Código de erro de substatus. |
| scWin32Status |Código de status do Windows. |
| sIP |Endereço IP do servidor Web. |
| SourceSystem |OpsMgr |
| sPort |Porta no servidor ao qual o cliente está conectado. |
| sSiteName |Nome do site do IIS. |
| TimeGenerated |Data e hora em que a entrada foi registrada. |
| TimeTaken |Período para processar a solicitação em milissegundos. |
| csHost | Nome do host. |
| csBytes | Número de bytes recebidos pelo servidor. |

## <a name="log-queries-with-iis-logs"></a>Consultas de log com logs do IIS
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros do log do IIS.

| Consulta | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registros de log do IIS. |
| W3CIISLog &#124; where scStatus==500 |Todos os registros de log do IIS com um status de retorno de 500. |
| W3CIISLog &#124; summarize count() by cIP |Contagem das entradas do log do IIS por endereço IP do cliente. |
| W3CIISLog &#124; em que csHost = = "www \. contoso.com" &#124; resume Count () por csUriStem |Contagem de entradas de log do IIS por URL para o host www \. contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Total de bytes recebidos por cada computador com IIS. |

## <a name="next-steps"></a>Próximas etapas
* Configure o Azure Monitor para coletar outras [fontes de dados](../agents/agent-data-sources.md) para análise.
* Saiba mais sobre [registrar consultas](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.
