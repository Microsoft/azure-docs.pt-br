---
title: Logs de IIS no Azure Monitor | Microsoft Docs
description: O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Azure Monitor.  Este artigo descreve como configurar a coleta de logs do IIS e os detalhes dos registros que eles criam no repositório do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 402cd4723791c0bc33db22c8857d1b785862f596
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614500"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Coletar logs II no Azure Monitor
O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Azure Monitor e armazenados como [dados de log](data-platform.md).

![Logs IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurando logs do IIS
O Azure Monitor coleta entradas de arquivos de log criados pelo IIS, por isso você deve [configurar o IIS para o registro em log](https://technet.microsoft.com/library/hh831775.aspx).

O Azure Monitor só oferece suporte a arquivos de log do IIS armazenados em formato W3C e não oferece suporte a campos personalizados ou a registro em Log Avançado do IIS. Ele não coleta logs no formato nativo IIS ou NCSA.

Configurar logs do IIS no Azure Monitor a partir de [menu de configurações avançadas](agent-data-sources.md#configuring-data-sources).  Não há nenhuma outra configuração necessária além da seleção de **Collect W3C format IIS log files**(Coletar arquivos de log do IIS no formato W3C).


## <a name="data-collection"></a>Coleta de dados
O Azure Monitor coleta entradas de log do IIS de cada agente a cada vez que o log é fechado e um novo será criado. Essa frequência é controlada pela configuração **agendamento de substituição de arquivo de Log** para o site do IIS que é uma vez por dia por padrão. Por exemplo, se as configurações são **por hora**, então, o Azure Monitor coletará o log de cada hora.  Se as configurações for **diária**, então, o Azure Monitor coletará o log de cada dia.


## <a name="iis-log-record-properties"></a>Propriedades de registro de log do IIS
Os registros log do IIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela a seguir:

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Computador |Nome do computador do qual o evento foi coletado. |
| cIP |Endereço IP do cliente. |
| csMethod |Método de solicitação, como GET ou POST. |
| csReferer |Site do qual o usuário seguiu um link para o site atual. |
| csUserAgent |O tipo de navegador do cliente. |
| csUserName |Nome do usuário autenticado que acessou o servidor. Os usuários anônimos são indicados por um hífen. |
| csUriStem |Destino da solicitação, como uma página da Web. |
| csUriQuery |Consulta, se houver, que o cliente estava tentando executar. |
| ManagementGroupName |Nome do grupo de gerenciamento para agentes do Operations Manager.  Para outros agentes, ele é AOI-\<ID do espaço de trabalho\> |
| RemoteIPCountry |País do endereço IP do cliente. |
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

## <a name="log-queries-with-iis-logs"></a>Consultas de log com logs do IIS
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros do log do IIS.

| Consultar | DESCRIÇÃO |
|:--- |:--- |
| W3CIISLog |Todos os registros de log do IIS. |
| W3CIISLog &#124; where scStatus==500 |Todos os registros de log do IIS com um status de retorno de 500. |
| W3CIISLog &#124; summarize count() by cIP |Contagem das entradas do log do IIS por endereço IP do cliente. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Entradas de log de contagem do IIS por URL para o host www\.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Total de bytes recebidos por cada computador com IIS. |

## <a name="next-steps"></a>Próximas etapas
* Configure o Azure Monitor para coletar outras [fontes de dados](agent-data-sources.md) para análise.
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.