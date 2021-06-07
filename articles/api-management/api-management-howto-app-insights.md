---
title: Integrar o gerenciamento de API do Azure com o Aplicativo Azure insights
titleSuffix: Azure API Management
description: Saiba como registrar e exibir eventos do Gerenciamento de API do Azure no Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564229"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar o Gerenciamento de API do Azure ao Azure Application Insights

O Gerenciamento de API do Azure permite uma integração fácil com o Azure Application Insights, um serviço abrangente para desenvolvedores para Web que criam e gerenciam aplicativos em diversas plataformas. Este guia orienta cada etapa dessa integração e descreve as estratégias para reduzir o impacto no desempenho da instância de serviço do Gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, é necessário ter uma instância do Gerenciamento de API do Azure. Caso não tenha, primeiro conclua o [tutorial](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Criar uma instância de Application Insights

Antes de poder usar Application Insights, primeiro você precisa criar uma instância do serviço. Para obter as etapas para criar uma instância usando o portal do Azure, consulte [recursos de Application insights baseados em espaço de trabalho](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Criar uma conexão entre Application Insights e gerenciamento de API

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure**.
1. Selecione **Application Insights** no menu à esquerda.
1. Clique em **+ Adicionar**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Captura de tela que mostra onde adicionar uma nova conexão":::
1. Selecione a instância do **Application Insights** já criada e forneça uma descrição resumida.
1. Clique em **Criar**.
1. Você acabou de criar um agente de Application Insights com uma chave de instrumentação. Agora, ele deve aparecer na lista.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Captura de tela que mostra onde exibir o agente do Application Insights recém-criado com chave de instrumentação":::

> [!NOTE]
> Nos bastidores, uma entidade [Agente](/rest/api/apimanagement/2019-12-01/logger/createorupdate) é criada sem sua instância do Gerenciamento de API, contendo a Chave de instrumentação da instância do Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Habilitar o log do Application Insights para sua API

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure**.
1. Selecione **APIs** no menu à esquerda.
1. Clique na API, nesse caso a **API de Conferência de Demonstração**. Se configurado, selecione uma versão.
1. Acesse a guia **Configurações** na barra superior.
1. Role para baixo até a seção **Logs de Diagnóstico**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Agente do Application Insights":::
1. Marque a caixa **Habilitar**.
1. Selecione o agente anexado na lista suspensa **Destino**.
1. Insira **100** como **amostragem (%)** e marque a caixa de seleção **sempre registrar erros** .
1. Clique em **Salvar**.

> [!WARNING]
> Substituir o valor padrão **0** na configuração **número de bytes de carga para log** pode diminuir significativamente o desempenho de suas APIs.

> [!NOTE]
> Nos bastidores, uma entidade [Diagnóstico](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) chamada 'applicationinsights' é criada no nível da API.

| Nome da configuração                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar                              | booleano                           | Especifica se o log desta API está habilitado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Agente do Azure Application Insights | Especifica o agente do Azure Application Insights a ser usado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (porcentagem). <br/> Especifica a porcentagem de solicitações que serão registradas no Application Insights. A amostragem de 0% significa zero solicitações registradas, enquanto a amostragem de 100% significa que todas as solicitações foram registradas. <br/> Use essa configuração para reduzir o efeito no desempenho ao registrar em log as solicitações para Application Insights. Consulte [implicações de desempenho e amostragem de log](#performance-implications-and-log-sampling). |
| Sempre registrar erros                   | booleano                           | Se essa configuração for selecionada, todas as falhas serão registradas em Application Insights, independentemente da configuração de **amostragem** .   
| Endereço IP do cliente de log | |  Se essa configuração for selecionada, o endereço IP do cliente para solicitações de API será registrado em Application Insights.                                         |
| Detalhamento         |                                   | Especifica o nível de verbosidade. Somente rastreamentos personalizados com nível de severidade mais alto serão registrados. Padrão: informações.      | 
| Protocolo de correlação |  |  Selecione o protocolo usado para correlacionar a telemetria enviada por vários componentes. Padrão: herdado <br/>Para obter informações, consulte [correlação de telemetria no Application insights](../azure-monitor/app/correlation.md).  |
| Opções básicas: cabeçalhos no log              | list                              | Especifica os cabeçalhos que serão registrados para Application Insights para solicitações e respostas.  Padrão: nenhum cabeçalho é registrado.                                                                                                                                                                                                             |
| Opções básicas: número de bytes de carga a serem registradas | inteiro                           | Especifica quantos primeiros bytes do corpo são registrados em Application Insights para solicitações e respostas.  Padrão: 0.                                                                                                                                                                                                    |
| Opções avançadas: solicitação de front-end  |                                   | Especifica se e como *as solicitações de front-end* serão registradas em Application insights. A *solicitação de front-end* é uma solicitação recebida no serviço de Gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de front-end |                                   | Especifica se e como as *respostas de front-end* serão registradas em Application insights. A *resposta de front-end* é uma resposta de saída no serviço de Gerenciamento de API do Azure.                                                                                                                                                                   |
| Opções avançadas: solicitação de back-end   |                                   | Especifica se e como *as solicitações de back-end* serão registradas em Application insights. A *solicitação de back-end* é uma solicitação de saída do serviço de Gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de back-end  |                                   | Especifica se e como as *respostas de back-end* serão registradas em Application insights. A *resposta de back-end* é uma resposta recebida no serviço de Gerenciamento de API do Azure.                                                                                                                                                                       |

> [!NOTE]
> Você pode especificar agentes em diferentes níveis, ou seja, um único agente de API ou um agente para todas as APIs.
>  
> Se você especificar as duas opções:
> + Se eles forem agentes diferentes, ambos serão usados (multiplexação de logs),
> + Se eles forem os mesmos agentes mas tiverem configurações diferentes, o agente da API única (nível mais granular) substituirá a configuração de todas as APIs.

## <a name="what-data-is-added-to-application-insights"></a>Quais dados são adicionados a Application Insights

Application Insights recebe:

+ Item de telemetria de *solicitação* de cada solicitação recebida (*solicitação de front-end*, *resposta de front-end*),
+ Item de telemetria de *dependência*, para cada solicitação encaminhada para um serviço de back-end (*solicitação de back-end*, *resposta de back-end*),
+ Item de telemetria de *exceção* , para cada solicitação com falha:
    + falha devido a uma conexão de cliente fechada
    + disparou uma seção *On-Error* das políticas de API
    + tem um código de status HTTP de resposta correspondente a 4xx ou a 5xx.
+ *Rastrear* item de telemetria, se você configurar uma política de [rastreamento](api-management-advanced-policies.md#Trace) . A `severity` configuração na `trace` política deve ser igual ou maior que a `verbosity` configuração no log de Application insights.

> [!NOTE]
> Confira [Application insights limites](../azure-monitor/service-limits.md#application-insights) para obter informações sobre o tamanho máximo e o número de métricas e eventos por Application insights instância.

## <a name="performance-implications-and-log-sampling"></a>Implicações no desempenho e amostragem de log

> [!WARNING]
> O registro de todos os eventos pode ter sérias implicações no desempenho, dependendo da taxa de solicitações recebidas.

Com base nos testes de carga internos, a habilitação desse recurso causou uma redução de 40% a 50% na produtividade quando a taxa de solicitação excedeu 1.000 solicitações por segundo. Application Insights foi projetado para usar a análise estatística para avaliar o desempenho do aplicativo. Ele não deve ser um sistema de auditoria e não é adequado para registrar cada solicitação individual para APIs de alto volume.

Você pode manipular o número de solicitações sendo registradas, ajustando a configuração de **amostragem** (consulte as etapas anteriores). Um valor de 100% significa que todas as solicitações são registradas, enquanto 0% não reflete nenhum registro em log. A **amostragem** ajuda a reduzir o volume de telemetria, evitando efetivamente a degradação significativa do desempenho, ao mesmo tempo em que ainda tem os benefícios do registro em log.

Ignorar o registro do cabeçalho e do corpo das solicitações e respostas também terá um impacto positivo atenuando problemas de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [Azure Application Insights](/azure/application-insights/).
+ Considere o [registro com Hubs de Eventos do Azure](api-management-howto-log-event-hubs.md).
