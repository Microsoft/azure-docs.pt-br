---
title: Logs do IIS no Log Analytics | Microsoft Docs
description: "O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Log Analytics.  Este artigo descreve como configurar a coleta de logs do IIS e os detalhes dos registros que eles criam no repositório do OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2017
ms.author: bwren
ms.openlocfilehash: 2114bdafb3b9fe2eb0632271840b8b70a76d10f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="iis-logs-in-log-analytics"></a>Logs do IIS no Log Analytics
O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Log Analytics.  

![Logs IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurando logs do IIS
O Log Analytics coleta entradas de arquivos de log criados pelo IIS, por isso você deve [configurar o IIS para o registro em log](https://technet.microsoft.com/library/hh831775.aspx).

O Log Analytics só oferece suporte a arquivos de log do IIS armazenados em formato W3C e não oferece suporte a campos personalizados ou a registro em Log Avançado do IIS.  
O Log Analytics não coleta logs no formato nativo IIS ou NCSA.

Configure os logs do IIS no [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Não há nenhuma outra configuração necessária além da seleção de **Collect W3C format IIS log files**(Coletar arquivos de log do IIS no formato W3C).

É recomendável, ao habilitar a coleta de log do IIS, definir a configuração de substituição de log do IIS em cada servidor.

## <a name="data-collection"></a>Coleta de dados
O Log Analytics coleta as entradas de log do IIS de cada fonte conectada a cada 15 minutos, aproximadamente.  O agente registra seu lugar em cada log de eventos do qual ele realiza a coleta.  Se o agente fica offline, o Log Analytics coleta os eventos de onde ele parou, mesmo que os eventos tenham sido criados enquanto o agente estava offline.

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

## <a name="log-searches-with-iis-logs"></a>Pesquisas de log com logs do IIS
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros do log do IIS.

| Consultar | Descrição |
|:--- |:--- |
| Type=W3CIISLog |Todos os registros de log do IIS. |
| Type=W3CIISLog scStatus=500 |Todos os registros de log do IIS com um status de retorno de 500. |
| Type=W3CIISLog &#124; Measure count() by cIP |Contagem das entradas do log do IIS por endereço IP do cliente. |
| Type=W3CIISLog csHost="www.contoso.com" &#124; Measure count() by csUriStem |Contagem das entradas de log do IIS por URL para o host www.contoso.com. |
| Type=W3CIISLog &#124; Measure Sum(csBytes) by Computer &#124; top 500000 |Total de bytes recebidos por cada computador com IIS. |

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.

> | Consultar | Descrição |
|:--- |:--- |
| W3CIISLog |Todos os registros de log do IIS. |
| W3CIISLog &#124; where scStatus==500 |Todos os registros de log do IIS com um status de retorno de 500. |
| W3CIISLog &#124; summarize count() by cIP |Contagem das entradas do log do IIS por endereço IP do cliente. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Contagem das entradas de log do IIS por URL para o host www.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Total de bytes recebidos por cada computador com IIS. |

## <a name="next-steps"></a>Próximas etapas
* Configure o Log Analytics para coletar outras [fontes de dados](log-analytics-data-sources.md) para análise.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.
* Configure alertas no Log Analytics para notificá-lo proativamente sobre condições importantes encontradas em logs do IIS.
