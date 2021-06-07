---
title: Monitorar operações e atividades
titleSuffix: Azure Cognitive Search
description: Habilite o registro em log, obtenha métricas de atividade de consulta, uso de recursos e outros dados do sistema de um serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: aa224a09317aafd49ae10c89ae0c50455ddd4602
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709915"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorar operações e atividades do Azure Pesquisa Cognitiva

Este artigo é uma visão geral de como monitorar os conceitos e as ferramentas do Azure Pesquisa Cognitiva. Para o monitoramento holístico, você pode usar uma combinação de funcionalidade interna e serviços complementares como Azure Monitor.

Completamente, você pode acompanhar o seguinte:

* Serviço: integridade/disponibilidade e alterações na configuração do serviço.
* Armazenamento: ambos usados e disponíveis, com contagens para cada tipo de conteúdo relativo à cota permitida para a camada de serviço.
* Atividade de consulta: volume, latência e consultas limitadas ou descartadas. Solicitações de consulta registradas exigem [Azure monitor](#add-azure-monitor).
* Atividade de indexação: requer [log de diagnóstico](#add-azure-monitor) com Azure monitor.

Um serviço de pesquisa não oferece suporte à autenticação por usuário, portanto, nenhuma informação de identidade será encontrada nos logs.

## <a name="built-in-monitoring"></a>Monitoramento interno

O monitoramento interno refere-se a atividades registradas em log por um serviço de pesquisa. Com exceção de diagnóstico, nenhuma configuração é necessária para esse nível de monitoramento.

O Azure Pesquisa Cognitiva mantém dados internos em um cronograma de 30 dias sem interrupção para relatórios sobre a integridade do serviço e métricas de consulta, que podem ser encontradas no portal ou por meio dessas [APIs REST](#monitoring-apis).

A captura de tela a seguir o ajuda a localizar informações de monitoramento no Portal. Os dados ficam disponíveis assim que você começa a usar o serviço. As páginas do portal são atualizadas a cada poucos minutos.

* Guia **monitoramento** , na página Visão geral principal, mostra volume de consulta, latência e se o serviço está sob pressão.
* O **log de atividades**, no painel de navegação esquerdo, está conectado a Azure Resource Manager. O log de atividades relata sobre as ações realizadas pelo Resource Manager: disponibilidade e status do serviço, alterações de capacidade (réplicas e partições) e atividades relacionadas à chave de API.
* As configurações de **monitoramento** , mais adiante, fornecem alertas configuráveis, visualização de métricas e logs de diagnóstico. Crie-os quando precisar deles. Depois que os dados são coletados e armazenados, você pode consultar ou visualizar as informações de insights.

  ![Integração de Azure Monitor em um serviço de pesquisa](./media/search-monitor-usage/azure-monitor-search.png
 "Integração de Azure Monitor em um serviço de pesquisa")

> [!NOTE]
> Como as páginas do portal são atualizadas a cada poucos minutos, os números relatados são aproximados, destinados a fornecer uma noção geral de como o sistema está atendendo às solicitações. As métricas reais, como consultas por segundo (QPS), podem ser maiores ou menores do que o número mostrado na página. Se a precisão for um requisito, considere o uso de APIs.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>APIs úteis para monitoramento

Você pode usar as seguintes APIs para recuperar as mesmas informações encontradas nas guias monitoramento e uso no Portal.

* [OBTER estatísticas de serviço](/rest/api/searchservice/get-service-statistics)
* [OBTER estatísticas de índice](/rest/api/searchservice/get-index-statistics)
* [OBTER contagens de documentos](/rest/api/searchservice/count-documents)
* [OBTER o status do indexador](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Logs de atividade e integridade do serviço

A página [**log de atividades**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) no portal coleta informações de Azure Resource Manager e relata sobre alterações na integridade do serviço. Você pode monitorar o log de atividades em busca de condições críticas, de erro e de aviso relacionadas à integridade do serviço.

As entradas comuns incluem referências a chaves de API – notificações informativas genéricas, como *obter chave de administração* e *obter chaves de consulta*. Essas atividades indicam solicitações que foram feitas usando a chave de administração (criar ou excluir objetos) ou a chave de consulta, mas não mostram a solicitação em si. Para obter informações sobre esse detalhamento, você deve configurar o log de diagnósticos.

Você pode acessar o **Log de atividades** no painel de navegação à esquerda, em Notificações na barra de comando da janela superior ou na página **Diagnosticar e solucionar problemas**.

### <a name="monitor-storage-in-the-usage-tab"></a>Monitorar o armazenamento na guia uso

Para o monitoramento visual no portal, a guia **uso** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md) atuais impostos pela camada de serviço. Se estiver decidindo sobre [qual camada usar para cargas de trabalho de produção](search-sku-tier.md), ou se quiser [ajustar o número de partições e réplicas ativas](search-capacity-planning.md), essas métricas podem ajudá-lo nessas decisões ao mostrar o quão rápido os recursos são consumidos e o quão bem a configuração atual lida com a carga existente.

A ilustração a seguir aplica-se ao serviço gratuito, que está limitado a três objetos de cada tipo e 50 MB de armazenamento. Um serviço Basic ou Standard tem limites maiores e, se você aumentar a quantidade de partições, o armazenamento máximo aumenta proporcionalmente.

![Status de uso relativo aos limites de camada](./media/search-monitor-usage/usage-tab.png
 "Status de uso relativo aos limites de camada")

> [!NOTE]
> Os alertas relacionados ao armazenamento não estão disponíveis no momento; o consumo de armazenamento não é agregado ou conectado à tabela **AzureMetrics** em Azure monitor. Para obter alertas de armazenamento, você precisa [criar uma solução personalizada](../azure-monitor/insights/solutions.md) que emita notificações relacionadas a recursos, em que seu código verifica o tamanho do armazenamento e manipula a resposta.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Monitoramento de complemento com o Azure Monitor

Muitos serviços, incluindo o Azure Pesquisa Cognitiva, são integrados com [Azure monitor](../azure-monitor/index.yml) para alertas adicionais, métricas e logs de dados de diagnóstico. 

[Habilite o log de diagnóstico](search-monitor-logs.md) para um serviço de pesquisa se você quiser ter controle sobre a coleta de dados e o armazenamento. Os eventos registrados pelo Azure Monitor são armazenados na tabela **AzureDiagnostics** e consistem em dados operacionais relacionados a consultas e à indexação.

Azure Monitor fornece várias opções de armazenamento e sua escolha determina como você pode consumir os dados:

* Escolha armazenamento de BLOBs do Azure se desejar [Visualizar dados de log](search-monitor-logs-powerbi.md) em um relatório de Power bi.
* Escolha Log Analytics se desejar explorar dados por meio de consultas Kusto.

Azure Monitor tem sua própria estrutura de cobrança e os logs de diagnóstico referenciados nesta seção têm um custo associado. Para obter mais informações, consulte [uso e custos estimados em Azure monitor](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Monitorar o acesso do usuário

Como os índices de pesquisa são um componente de um aplicativo cliente maior, não há nenhuma metodologia interna para controlar ou monitorar o acesso por usuário a um índice. As solicitações são consideradas provenientes de um aplicativo cliente que apresenta uma solicitação de administrador ou de consulta. As operações de leitura/gravação do administrador incluem a criação, a atualização e a exclusão de objetos em todo o serviço. Operações somente leitura são consultas em relação à coleção de documentos, com escopo para um único índice. 

Assim, o que você verá nos logs de atividade são referências a chamadas usando chaves de administração ou chaves de consulta. A chave apropriada é incluída em solicitações originadas do código do cliente. O serviço não está equipado para manipular tokens de identidade ou representação.

Quando os requisitos de negócios existem para autorização por usuário, a recomendação é a integração com o Azure Active Directory. Você pode usar $filter e identidades de usuário para [aparar os resultados da pesquisa](search-security-trimming-for-azure-search-with-aad.md) de documentos que um usuário não deve ver. 

Não é possível registrar essas informações separadamente da cadeia de caracteres de consulta que inclui o parâmetro $filter. Consulte [monitorar consultas](search-monitor-queries.md) para obter detalhes sobre cadeias de caracteres de consulta de relatórios.

## <a name="next-steps"></a>Próximas etapas

O fluência com Azure Monitor é essencial para supervisão de qualquer serviço do Azure, incluindo recursos como o Pesquisa Cognitiva do Azure. Se você não estiver familiarizado com Azure Monitor, Reserve um tempo para revisar os artigos relacionados aos recursos. Além dos tutoriais, o artigo a seguir é um bom ponto de partida.

> [!div class="nextstepaction"]
> [Monitorar recursos do Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)