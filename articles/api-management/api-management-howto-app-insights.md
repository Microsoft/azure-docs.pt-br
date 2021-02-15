---
title: Integrar o gerenciamento de API do Azure com o Aplicativo Azure insights
titleSuffix: Azure API Management
description: Saiba como registrar e exibir eventos do Gerenciamento de API do Azure no Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: adb221c12af436135b1e740fdef7c5c0a0a7f0cb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096029"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar o Gerenciamento de API do Azure ao Azure Application Insights

O Gerenciamento de API do Azure permite uma integração fácil com o Azure Application Insights, um serviço abrangente para desenvolvedores para Web que criam e gerenciam aplicativos em diversas plataformas. Este guia orienta cada etapa dessa integração e descreve as estratégias para reduzir o impacto no desempenho da instância de serviço do Gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, é necessário ter uma instância do Gerenciamento de API do Azure. Caso não tenha, primeiro conclua o [tutorial](get-started-create-service-instance.md).

## <a name="create-an-azure-application-insights-instance"></a>Criar uma instância do Azure Application Insights

Para que você possa usar as informações do Azure Application Insights, primeiro você precisa criar uma instância do serviço.

1. Abra o **portal do Azure** e navegue até o **Application Insights** .  
    ![Captura de tela que mostra como navegar até Application Insights.](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Clique em **+ Adicionar** .  
    ![Criar Application Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Preencha o formulário. Selecione **Geral** como o **Tipo de Aplicativo** .
4. Clique em **Criar** .

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Criar uma conexão entre a instância de serviço do Azure Application Insights e o Gerenciamento de API do Azure

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure** .
2. Selecione **Application Insights** no menu à esquerda.
3. Clique em **+ Adicionar** .  
    ![Captura de tela que mostra onde adicionar uma nova conexão.](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecione a instância do **Application Insights** já criada e forneça uma descrição resumida.
5. Clique em **Criar** .
6. Você acabou de criar um agente do Azure Application Insights com uma chave de instrumentação. Agora, ele deve aparecer na lista.  
    ![Captura de tela que mostra onde exibir o agente de log de informações recém-criado Aplicativo Azure com chave de instrumentação.](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Nos bastidores, uma entidade [Agente](/rest/api/apimanagement/2019-12-01/logger/createorupdate) é criada sem sua instância do Gerenciamento de API, contendo a Chave de instrumentação da instância do Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Habilitar o log do Application Insights para sua API

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure** .
2. Selecione **APIs** no menu à esquerda.
3. Clique na API, nesse caso a **API de Conferência de Demonstração** .
4. Acesse a guia **Configurações** na barra superior.
5. Role para baixo até a seção **Logs de Diagnóstico** .  
    ![Agente do Application Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Marque a caixa **Habilitar** .
7. Selecione o agente anexado na lista suspensa **Destino** .
8. Insira **100** como **Amostragem (%)** e marque a caixa de seleção **Sempre registrar erros** .
9. Clique em **Salvar** .

> [!WARNING]
> Substituir o valor padrão **0** no campo **Primeiros bytes do corpo** pode diminuir significativamente o desempenho de suas APIs.

> [!NOTE]
> Nos bastidores, uma entidade [Diagnóstico](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) chamada 'applicationinsights' é criada no nível da API.

| Nome da configuração                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar                              | booleano                           | Especifica se o log desta API está habilitado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Agente do Azure Application Insights | Especifica o agente do Azure Application Insights a ser usado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (porcentagem). <br/> Especifica o percentual de solicitações que será registrada no Azure Application Insights. A amostragem de 0% significa zero solicitações registradas, enquanto a amostragem de 100% significa que todas as solicitações foram registradas. <br/> Essa configuração é usada para reduzir as implicações no desempenho das solicitações de registro no Azure Application Insights (confira a seção abaixo). |
| Sempre registrar erros                   | booleano                           | Se essa configuração for selecionada, todas as falhas serão registradas no Azure Application Insights, independentemente da configuração de **Amostragem** .                                                                                                                                                                                                                  |
| Opções básicas: cabeçalhos              | list                              | Especifica os cabeçalhos que serão registrados no Azure Application Insights para solicitações e respostas.  Padrão: nenhum cabeçalho é registrado.                                                                                                                                                                                                             |
| Opções básicas: primeiros bytes do corpo  | inteiro                           | Especifica quantos primeiros bytes do corpo são registrados no Azure Application Insights para solicitações e respostas.  Padrão: o corpo não é registrado.                                                                                                                                                                                                    |
| Opções avançadas: detalhes         |                                   | Especifica o nível de verbosidade. Somente rastreamentos personalizados com nível de severidade mais alto serão registrados. Padrão: informações.                                                                                                                                                                                                                               |
| Opções avançadas: solicitação de front-end  |                                   | Especifica se e como as *solicitações de front-end* serão registradas no Azure Application Insights. A *solicitação de front-end* é uma solicitação recebida no serviço de Gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de front-end |                                   | Especifica se e como as *respostas de front-end* serão registradas no Azure Application Insights. A *resposta de front-end* é uma resposta de saída no serviço de Gerenciamento de API do Azure.                                                                                                                                                                   |
| Opções avançadas: solicitação de back-end   |                                   | Especifica se e como as *solicitações de back-end* serão registradas no Azure Application Insights. A *solicitação de back-end* é uma solicitação de saída do serviço de Gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de back-end  |                                   | Especifica se e como as *respostas de back-end* serão registradas no Azure Application Insights. A *resposta de back-end* é uma resposta recebida no serviço de Gerenciamento de API do Azure.                                                                                                                                                                       |

> [!NOTE]
> Você pode especificar agentes em diferentes níveis, ou seja, um único agente de API ou um agente para todas as APIs.
>  
> Se você especificar as duas opções:
> + Se eles forem agentes diferentes, ambos serão usados (multiplexação de logs),
> + Se eles forem os mesmos agentes mas tiverem configurações diferentes, o agente da API única (nível mais granular) substituirá a configuração de todas as APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Quais dados são adicionados ao Azure Application Insights

O Azure Application Insights recebe:

+ Item de telemetria de *solicitação* de cada solicitação recebida ( *solicitação de front-end* , *resposta de front-end* ),
+ Item de telemetria de *dependência* , para cada solicitação encaminhada para um serviço de back-end ( *solicitação de back-end* , *resposta de back-end* ),
+ Item de telemetria de *exceção* , para cada solicitação com falha.

Uma solicitação com falha é aquela que:

+ falhou porque uma conexão de cliente foi fechada,
+ disparou uma seção *em erro* das políticas de API ou
+ tem um código de status HTTP de resposta correspondente a 4xx ou a 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicações no desempenho e amostragem de log

> [!WARNING]
> O registro de todos os eventos pode ter sérias implicações no desempenho, dependendo da taxa de solicitações recebidas.

Com base nos testes de carga internos, a habilitação desse recurso causou uma redução de 40% a 50% na produtividade quando a taxa de solicitação excedeu 1.000 solicitações por segundo. O Azure Application Insights foi projetado para usar a análise estatística para avaliar o desempenho do aplicativo. Ele não deve ser um sistema de auditoria e não é adequado para registrar cada solicitação individual para APIs de alto volume.

Você pode manipular o número de solicitações que estão sendo registradas, ajustando a configuração **Amostragem** (confira as etapas acima). O valor 100% significa que todas as solicitações são registradas, enquanto 0% não reflete nenhum registro. **Amostragem** ajuda a reduzir o volume de telemetria, evitando efetivamente a degradação significativa do desempenho, enquanto continua carregando os benefícios do registro em log.

Ignorar o registro do cabeçalho e do corpo das solicitações e respostas também terá um impacto positivo atenuando problemas de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [Azure Application Insights](/azure/application-insights/).
+ Considere o [registro com Hubs de Eventos do Azure](api-management-howto-log-event-hubs.md).
