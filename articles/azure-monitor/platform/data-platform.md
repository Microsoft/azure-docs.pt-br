---
title: Plataforma de dados do Azure Monitor | Microsoft Docs
description: Os dados de monitoramento coletados pelo Azure Monitor são separados em métricas leves e que podem dar suporte a logs e cenários quase em tempo real que são usados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808944"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados do Azure Monitor

A habilitação de Observação entre complexos ambientes computacionais atuais em execução de aplicativos distribuídos que se baseiam na nuvem e serviços no local, requer a coleta de dados operacionais de todas as camadas e todos os componentes de um sistema distribuído. Você precisa ser capaz de executar insights profundos sobre esses dados e consolidá-lo em um único painel de vidro com diferentes perspectivas para dar suporte a variedade das partes interessadas na sua organização.

[O Azure Monitor](../overview.md) coleta e agrega dados de uma variedade de fontes em uma plataforma de dados comum em que ele pode ser usado para análise, visualização e alertas. Ele fornece uma experiência consistente sobre os dados de várias fontes, que lhe dá um aprofundamento em todos os seus recursos monitorados e até mesmo com os dados de outros serviços que armazenam seus dados no Azure Monitor.


![Visão geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observação no Azure Monitor
As métricas, logs e rastreamentos distribuídos são conhecidos como os três pilares da observação. Esses são os diferentes tipos de dados que uma ferramenta de monitoramento deve coletar e analisar para fornecer uma observação suficiente de um sistema monitorado. Observação pode ser obtida por correlacionar os dados de vários pilares e agregar dados em todo o conjunto de recursos que estão sendo monitorados. Como o Azure Monitor armazena dados de várias fontes em conjunto, os dados possam ser correlacionados e analisados usando um conjunto comum de ferramentas. Ele correlaciona dados em várias assinaturas do Azure e locatários, além de hospedar dados para outros serviços.

Recursos do Azure geram uma quantidade significativa de dados de monitoramento. O Azure Monitor consolida esses dados, juntamente com os dados de outras fontes de monitoramento para um uma plataforma de métricas ou Logs. Cada um é otimizada para cenários de monitoramento específicos, e cada um oferece suporte a recursos diferentes no Azure Monitor. Recursos como análise de dados, visualizações ou alertas exigem que você a compreender as diferenças, para que você possa implementar seu cenário necessário da maneira mais eficiente e econômica. Insights no Azure Monitor, como [Application Insights](../app/app-insights-overview.md) ou [do Azure Monitor para VMs](../insights/vminsights-overview.md) têm ferramentas de análise que permitem que você se concentre no cenário de monitoramento específico sem precisar compreender os diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
As [Métricas](data-platform-metrics.md) são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Eles são coletados em intervalos regulares e são identificados com um carimbo de hora, um nome, um valor e um ou mais rótulos de definição. As métricas podem ser agregadas usando uma variedade de algoritmos, em comparação com outras métricas e analisados tendências ao longo do tempo. 

Métricas no Azure Monitor são armazenadas em um banco de dados de série temporal que é otimizado para análise de dados de carimbo de data / hora. Isso torna as métricas particularmente adequado para o alerta e rápida a detecção de problemas. Eles podem lhe dizer como está o desempenho do seu sistema, mas normalmente precisam ser combinados com os logs para identificar a causa raiz dos problemas.

As métricas estão disponíveis para análise interativa no portal do Azure com [Metrics Explorer](../app/metrics-explorer.md). Eles podem ser adicionados a um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados e usados para quase em tempo real [alertas](alerts-metric.md).

Leia mais sobre métricas do Azure Monitor, incluindo suas fontes de dados no [métricas no Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Logs
[Logs](data-platform-logs.md) são eventos que ocorreram dentro do sistema. Eles podem conter diferentes tipos de dados e podem ser estruturados ou texto do formulário com um carimbo de hora livre. Eles podem ser criados esporadicamente conforme eventos no ambiente de geram entradas de log e um sistema sob carga pesada geralmente gera mais volume de log.

Logs no Azure Monitor são armazenados em um espaço de trabalho do Log Analytics com base no [Data Explorer do Azure](/azure/data-explorer/) que fornece um mecanismo de análise poderosas e [linguagem de consulta avançada](/azure/kusto/query/). Logs normalmente fornecem informações suficientes para fornecer o contexto completo do problema que está sendo identificado e são importantes para identificar a causa raiz dos problemas.

> [!NOTE]
> É importante distinguir entre Logs do Azure Monitor e fontes de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados para uma [log de atividades](activity-logs-overview.md) que podem ser exibidos no menu do Azure Monitor. A maioria dos recursos será grava informações operacionais para uma [log de diagnóstico](diagnostic-logs-overview.md) que você pode encaminhar para locais diferentes. Os Logs do Azure Monitor é uma plataforma de dados de log que coleta os logs de atividade e logs de diagnóstico junto com outros dados de monitoramento para fornecer análise profunda de todo o conjunto de recursos.


 Você pode trabalhar com [consultas de log](../log-query/log-query-overview.md) interativamente com [Log Analytics](../log-query/portals.md) no portal do Azure ou adicionar os resultados em um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Você também pode criar [alertas de log](alerts-log.md) que irá disparar um alerta com base nos resultados de uma consulta de agendamento.

Leia mais sobre Logs do Azure Monitor, incluindo suas fontes de dados no [registra em log no Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Rastreamentos distribuídos
Os rastreamentos são uma série de eventos relacionados que seguem uma solicitação de usuário por meio de um sistema distribuído. Eles podem ser usados para determinar o comportamento do código do aplicativo e o desempenho de transações diferentes. Embora os logs serão criados com frequência por componentes individuais de um sistema distribuído, um rastreamento mede a operação e o desempenho do seu aplicativo em todo o conjunto de componentes.

Rastreamento distribuído no Azure Monitor está habilitado com o [SDK do Application Insights](../app/distributed-tracing.md), e os dados de rastreamento são armazenados com outros dados de log do aplicativo coletados pelo Application Insights. Isso torna disponível para as mesmas ferramentas de análise que outros dados de log, incluindo consultas de log, painéis e alertas.

Leia mais sobre o rastreamento em distribuído [o que é o rastreamento distribuído?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Comparar os Logs e métricas do Azure Monitor

A tabela a seguir compara as métricas e Logs no Azure Monitor.

| Atributo  | Métricas | Logs |
|:---|:---|:---|
| Benefícios | Leve e capaz de cenários de quase em tempo real, como alertas. Ideal para a detecção rápida de problemas. | Analisados com avançada linguagem de consulta. Ideal para uma análise profunda e identificar a causa raiz. |
| Dados | Valores numéricos apenas | Dados numéricos ou de texto |
| Estrutura | Conjunto padrão de propriedades, incluindo o tempo de amostragem, recursos que estão sendo monitorados, um valor numérico. Algumas métricas incluem várias dimensões para definição adicional. | Conjunto de propriedades dependendo do tipo de log exclusivo. |
| Coleção | Coletados em intervalos regulares. | Podem ser coletadas esporadicamente, como eventos de gatilho um registro a ser criado. |
| Exibir no portal do Azure | Metrics Explorer | Log Analytics |
| Incluem fontes de dados | Métricas de plataforma coletados dos recursos do Azure.<br>Aplicativos monitorados pelo Application Insights.<br>Personalizado definido pelo aplicativo ou API. | Aplicativo e os Logs de diagnóstico.<br>Soluções de monitoramento.<br>Os agentes e extensões de VM.<br>O aplicativo solicita e exceções.<br>Central de segurança do Azure.<br>API do coletor de dados. |

## <a name="collect-monitoring-data"></a>Coletar dados de monitoramento
Diferentes [fontes de dados para o Azure Monitor](data-sources.md) gravará a um espaço de trabalho do Log Analytics (Logs) ou o banco de dados do Azure Monitor métricas (métricas) ou ambos. Algumas fontes gravará diretamente esses armazenamentos de dados, enquanto outros podem gravar em outro local, como o armazenamento do Azure e exigem alguma configuração para preencher os logs ou métricas. 

Ver [métricas no Azure Monitor](data-platform-metrics.md) e [registra em log no Azure Monitor](data-platform-logs.md) para obter uma lista de diferentes fontes de dados que preenchem a cada tipo.


## <a name="stream-data-to-external-systems"></a>Transmitir dados para sistemas externos
Além de usar as ferramentas no Azure para analisar os dados de monitoramento, pode haver a exigência de encaminhá-los para uma ferramenta externa, como um produto SIEM (gerenciamento de eventos e informações de segurança). Esse encaminhamento normalmente é feito diretamente de recursos monitorados por meio dos [Hubs de Eventos](/azure/event-hubs/). Algumas fontes podem ser configurados para enviar dados diretamente para um hub de eventos, embora você possa usar outro processo como um aplicativo lógico para recuperar os dados necessários. Ver [Azure Stream dados de monitoramento para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) para obter detalhes.



## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre [métricas no Azure Monitor](data-platform-metrics.md).
- Leia mais sobre [registra em log no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
