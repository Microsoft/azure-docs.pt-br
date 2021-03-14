---
title: Plataforma de dados do Azure Monitor | Microsoft Docs
description: Os dados de monitoramento coletados pelo Azure Monitor são separados em métricas leves e que podem dar suporte a logs e cenários quase em tempo real que são usados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731690"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados do Azure Monitor

Habilitar a observabilidade nos ambientes de computação complexos de hoje que executam aplicativos distribuídos que dependem de serviços de nuvem e locais exige a coleta de dados operacionais de todas as camadas e de todos os componentes do sistema distribuído. Você precisa conseguir se aprofundar nesses dados e consolidá-los em um só painel com perspectivas diferentes para dar suporte à infinidade de stakeholders na sua organização.

O [Azure Monitor](overview.md) coleta e agrega dados de uma variedade de fontes em uma plataforma de dados comum, em que ela pode ser usada para análise, visualização e alertas. Ele fornece uma experiência consistente com base em dados de várias fontes, que fornece insights aprofundados sobre todos os seus recursos monitorados e, até mesmo, com os dados de outros serviços que armazenam os respectivos dados no Azure Monitor.


![Visão geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observabilidade no Azure Monitor
Métricas, logs e rastreamentos distribuídos são conhecidos como os três pilares da observabilidade. Esses são os diferentes tipos de dados que uma ferramenta de monitoramento precisa coletar e analisar para proporcionar uma observabilidade suficiente de um sistema monitorado. A observabilidade pode ser obtida correlacionando os dados de vários pilares e agregando-os em todo o conjunto de recursos que estão sendo monitorados. Como o Azure Monitor armazena dados de várias fontes juntas, os dados podem ser correlacionados e analisados usando um conjunto comum de ferramentas. Ele também correlaciona dados em várias assinaturas e locatários do Azure, além de hospedar dados para outros serviços.

Os recursos do Azure geram um volume significativo de dados de monitoramento. O Azure Monitor consolida esses dados junto com os dados de monitoramento de outras fontes em uma plataforma de Métricas ou Logs. Cada uma delas é otimizada para cenários de monitoramento específicos e dá suporte a diferentes recursos no Azure Monitor. Recursos como análise de dados, visualizações ou alertas exigem a compreensão das diferenças para implementar o cenário necessário da maneira mais eficiente e econômica. As informações em Azure Monitor como o [Application insights](app/app-insights-overview.md) ou o [Revisions de VM](vm/vminsights-overview.md) têm ferramentas de análise que permitem que você se concentre no cenário de monitoramento específico sem ter que entender as diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
As [Métricas](essentials/data-platform-metrics.md) são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Elas são coletadas em intervalos regulares e são identificadas com um carimbo de data/hora, um nome, um valor e um ou mais rótulos de definição. As métricas podem ser agregadas por meio de uma variedade de algoritmos, comparadas com outras métricas e analisadas quanto às tendências ao longo do tempo. 

As métricas do Azure Monitor são armazenadas em um banco de dados de série temporal que é otimizado para analisar dados com carimbo de data/hora. Isso torna as métricas especialmente adequadas para alertas e detecção rápida de problemas. Elas podem informar sobre o desempenho do sistema, mas normalmente precisam ser combinadas com os logs para identificar a causa raiz dos problemas.

As métricas estão disponíveis para análise interativa no portal do Azure com o [Azure Metrics Explorer](essentials/metrics-getting-started.md). Elas podem ser adicionadas a um [painel do Azure](app/tutorial-app-dashboards.md) para visualização, em combinação com outros dados e usadas para [alertas quase em tempo real](alerts/alerts-metric.md).

Leia mais sobre as Métricas do Azure Monitor, incluindo as fontes de dados em [Métricas no Azure Monitor](essentials/data-platform-metrics.md).

### <a name="logs"></a>Logs
Os [logs](logs/data-platform-logs.md) são eventos que ocorreram no sistema. Eles podem conter diferentes tipos de dados e podem ser um texto de forma estruturada ou de forma livre com um carimbo de data/hora. Eles podem ser criados esporadicamente, pois os eventos no ambiente geram entradas de log, e um sistema sob carga pesada geralmente vai gerar um volume maior de log.

Os logs do Azure Monitor são armazenados em um workspace do Log Analytics baseado no [Azure Data Explorer](/azure/data-explorer/), que fornece um mecanismo de análise avançado e uma [linguagem de consulta avançada](/azure/kusto/query/). Os logs normalmente fornecem informações suficientes para fornecer o contexto completo do problema identificado e são úteis para identificar a causa raiz dos problemas.

> [!NOTE]
> É importante diferenciar os Logs do Azure Monitor e as origens de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados em um [log de atividades](essentials/platform-logs-overview.md) que pode ser visto no menu do Azure Monitor. A maioria dos recursos gravam informações operacionais em um [log de recursos](essentials/platform-logs-overview.md) que pode ser encaminhado para diferentes locais. Os Logs do Azure Monitor são uma plataforma de dados de log que coleta logs de atividade e de recursos junto com outros dados de monitoramento para fornecer uma análise profunda de todo o seu conjunto de recursos.


 Trabalhe com as [consultas de log](logs/log-query-overview.md) de maneira interativa com o [Log Analytics](logs/log-query-overview.md) no portal do Azure ou adicione os resultados a um [painel do Azure](app/tutorial-app-dashboards.md) para visualização, em combinação com outros dados. Crie também [alertas de log](alerts/alerts-log.md), que disparam um alerta com base nos resultados de uma consulta de agendamento.

Leia mais sobre os Logs do Azure Monitor, incluindo as fontes de dados em [Logs no Azure Monitor](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Rastreamentos distribuídos
Os rastreamentos são uma série de eventos relacionados que seguem uma solicitação de usuário por meio de um sistema distribuído. Eles podem ser usados para determinar o comportamento do código do aplicativo e o desempenho de transações diferentes. Embora os logs sejam frequentemente criados por componentes individuais de um sistema distribuído, um rastreamento mede a operação e o desempenho do seu aplicativo em todo o conjunto de componentes.

O rastreamento distribuído do Azure Monitor é habilitado com o [SDK do Application Insights](app/distributed-tracing.md) e os dados de rastreamento são armazenados com outros dados de log do aplicativo coletados pelo Application Insights. Isso os disponibiliza para as mesmas ferramentas de análise de outros dados de log, incluindo consultas de log, painéis e alertas.

Leia mais sobre o rastreamento distribuído em [O que é o rastreamento distribuído?](app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Comparar as Métricas e os Logs do Azure Monitor

A tabela a seguir compara as Métricas e os Logs do Azure Monitor.

| Atributo  | Métricas | Logs |
|:---|:---|:---|
| Benefícios | Leves e com capacidade de dar suporte a cenários quase em tempo real, como alertas. Ideais para detecção rápida de problemas. | Analisados com a linguagem de consulta avançada. Ideais para análise profunda e identificação da causa raiz. |
| Dados | Somente valores numéricos | Dados de texto ou numéricos |
| Estrutura | Conjunto padrão de propriedades, incluindo o tempo de amostragem, o recurso que está sendo monitorado, um valor numérico. Algumas métricas incluem várias dimensões para definição adicional. | Conjunto exclusivo de propriedades, dependendo do tipo de log. |
| Coleção | Coletadas em intervalos regulares. | Podem ser coletados esporadicamente, pois os eventos disparam um registro a ser criado. |
| Exibir no portal do Azure | Metrics Explorer | Log Analytics |
| As fontes de dados incluem | Métricas da plataforma coletadas dos recursos do Azure.<br>Aplicativos monitorados pelo Application Insights.<br>Definidas de maneira personalizada por aplicativo ou API. | Logs do aplicativo e de recursos.<br>Soluções de monitoramento.<br>Agentes e extensões de VM.<br>Solicitações e exceções do aplicativo.<br>Central de Segurança do Azure.<br>API do Coletor de Dados. |

## <a name="collect-monitoring-data"></a>Coletar dados de monitoramento
Diferentes [fontes de dados para o Azure Monitor](agents/data-sources.md) serão gravadas em um workspace do Log Analytics (Logs) ou no banco de dados de métricas do Azure Monitor (Métricas) ou em ambos. Algumas fontes serão gravadas diretamente nesses armazenamentos de dados, enquanto outras podem ser gravadas em outra localização, como o Armazenamento do Azure, e exigem alguma configuração para preencher os logs ou as métricas. 

Confira [Métricas no Azure Monitor](essentials/data-platform-metrics.md) e [Logs no Azure Monitor](logs/data-platform-logs.md) para obter uma lista de diferentes fontes de dados que preenchem cada tipo.


## <a name="stream-data-to-external-systems"></a>Transmitir dados para sistemas externos
Além de usar as ferramentas no Azure para analisar os dados de monitoramento, pode haver a exigência de encaminhá-los para uma ferramenta externa, como um produto SIEM (gerenciamento de eventos e informações de segurança). Esse encaminhamento normalmente é feito diretamente de recursos monitorados por meio dos [Hubs de Eventos](../event-hubs/index.yml). Algumas fontes podem ser configuradas para enviar dados diretamente a um hub de eventos, embora você possa usar outro processo, como um aplicativo lógico, para recuperar os dados necessários. Confira [Transmitir os dados de monitoramento do Azure para um hub de eventos para consumo por meio de uma ferramenta externa](essentials/stream-monitoring-data-event-hubs.md) para obter detalhes.



## <a name="next-steps"></a>Próximas etapas

- Leia sobre as [Métricas no Azure Monitor](essentials/data-platform-metrics.md).
- Leia mais sobre os [Logs no Azure Monitor](logs/data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](agents/data-sources.md) para diferentes recursos no Azure.

