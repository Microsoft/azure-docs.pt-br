---
title: "Conectores webhook para Aplicativo Lógico do Azure | Microsoft Docs"
description: "Como usar gatilhos e ações de webhook para realizar ações como Filtrar Matriz de aplicativos lógicos"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: fbfef291334109c6dcfcde80741874549fb7929f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-webhook-connector"></a>Introdução ao conector de webhook

Com a ação e o gatilho do webhook, você pode disparar, pausar e retomar fluxos para realizar estas tarefas:

* Disparar de um [Hub de Eventos do Azure](https://github.com/logicappsio/EventHubAPI) assim que um item é recebido
* Aguardar uma aprovação antes de continuar um fluxo de trabalho

Saiba mais sobre [como criar APIs personalizadas que dão suporte a um webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Usar o gatilho de webhook

Um [*gatilho*](connectors-overview.md) é um evento que inicia um fluxo de trabalho do aplicativo lógico. Um gatilho de webhook é baseado em evento e não depende de sondagem para novos itens. Assim como o [gatilho de solicitação](connectors-native-reqres.md), o aplicativo lógico é acionado no instante em que um evento ocorre. O gatilho de webhook registra uma *URL de retorno de chamada* em um serviço e usa essa URL para acionar o aplicativo lógico conforme necessário.

Veja um exemplo que mostra como configurar um gatilho HTTP no Designer de Aplicativo Lógico. Essas etapas pressupõem que você já implantou ou está acessando uma API que segue o [padrão de assinatura e cancelamento de assinatura de webhook encontrado nos aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). A chamada à assinatura é feita sempre que um aplicativo lógico é salvo com um novo webhook ou alternado de desabilitado para habilitado. A chamada ao cancelamento da assinatura é feita quando um gatilho de webhook do aplicativo lógico é removido e salvo ou alternado de habilitado para desabilitado.

**Para adicionar o gatilho de webhook**

1. Adicione o gatilho de **Webhook HTTP** como a primeira etapa em um aplicativo lógico.
2. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook.

   Isso segue o mesmo padrão que o formato da [ação HTTP](connectors-native-http.md).

     ![Gatilho de HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Adicione pelo menos uma ação.
4. Clique em **Salvar** para publicar o aplicativo lógico. Essa etapa chama o ponto de extremidade de assinatura com a URL de retorno de chamada necessário para disparar esse aplicativo lógico.
5. Sempre que o serviço fizer um `HTTP POST` para a URL de retorno de chamada, o aplicativo lógico será disparado (e incluirá todos os dados passados na solicitação).

## <a name="use-the-webhook-action"></a>Usar a ação de webhook

Uma [*ação*](connectors-overview.md) é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. Uma ação de webhook registra uma *URL de retorno de chamada* com um serviço e aguarda até que a URL seja chamada antes continuar. ["Enviar Email de Aprovação"](connectors-create-api-office365-outlook.md) é um exemplo de conector que segue esse padrão. Você pode estender esse padrão para qualquer serviço por meio de ação de webhook. 

Veja um exemplo que mostra como configurar uma ação de webhook no Designer de Aplicativo Lógico. Essas etapas pressupõem que você já implantou ou está acessando uma API que segue o [padrão de assinatura e cancelamento de assinatura de webhook usado em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions). A chamada à assinatura é feita sempre que um aplicativo lógico executa a ação de webhook. A chamada ao cancelamento da assinatura é feita sempre que uma execução é cancelada enquanto aguarda uma resposta ou antes que o tempo limite do aplicativo lógico seja atingido.

**Para adicionar uma ação de webhook**

1. Escolha **Nova Etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, digite "webhook" para localizar a ação **Webhook HTTP**.

    ![Selecionar ação de consulta](./media/connectors-native-webhook/using-action-1.png)

3. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook

   Isso segue o mesmo padrão que o formato da [ação HTTP](connectors-native-http.md).

     ![Concluir ação de consulta](./media/connectors-native-webhook/using-action-2.png)
   
   Em tempo de execução, o aplicativo lógico chama o ponto de extremidade de assinatura depois de atingir essa etapa.

4. Clique em **Salvar** para publicar o aplicativo lógico.

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão mais detalhes sobre os gatilhos e ações aos quais o webhook dá suporte.

## <a name="webhook-triggers"></a>Gatilhos de Webhook

| Ação | Descrição |
| --- | --- |
| Webhook HTTP |Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade. |

### <a name="trigger-details"></a>Detalhes do gatilho

#### <a name="http-webhook"></a>Webhook HTTP

Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade.
Um * significa que o campo é obrigatório.

| Nome de exibição | Nome da Propriedade | Descrição |
| --- | --- | --- |
| Método de Assinatura* |estático |Método HTTP a ser usado para solicitação de assinatura |
| URI da Assinatura* |uri |URI do HTTP a ser usado para solicitação de assinatura |
| Método de Cancelamento da Assinatura* |estático |Método HTTP a ser usado para solicitação de cancelamento da assinatura |
| URI do Cancelamento da Assinatura* |uri |URI do HTTP a ser usado para solicitação de cancelamento da assinatura |
| Corpo da Assinatura |corpo |Corpo da solicitação HTTP da assinatura |
| Cabeçalhos da Assinatura |headers |Cabeçalhos da solicitação HTTP da assinatura |
| Autenticação da Assinatura |Autenticação |Autenticação HTTP a ser usada para assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |
| Corpo do Cancelamento da Assinatura |corpo |Corpo da solicitação HTTP do cancelamento da assinatura |
| Cabeçalhos do Cancelamento da Assinatura |headers |Cabeçalhos da solicitação HTTP do cancelamento da assinatura |
| Autenticação do Cancelamento da Assinatura |autenticação |Autenticação HTTP a ser usada para cancelamento da assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |

**Detalhes da saída**

Solicitação de webhook

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| headers |objeto |Cabeçalhos da solicitação de webhook |
| corpo |objeto |Objeto da solicitação de webhook |
| Código de status |int |Código de status da solicitação de webhook |

## <a name="webhook-actions"></a>Ações de Webhook

| Ação | Descrição |
| --- | --- |
| Webhook HTTP |Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade. |

### <a name="action-details"></a>Detalhes da ação

#### <a name="http-webhook"></a>Webhook HTTP

Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade.
Um * significa que o campo é obrigatório.

| Nome de exibição | Nome da Propriedade | Descrição |
| --- | --- | --- |
| Método de Assinatura* |estático |Método HTTP a ser usado para solicitação de assinatura |
| URI da Assinatura* |uri |URI do HTTP a ser usado para solicitação de assinatura |
| Método de Cancelamento da Assinatura* |estático |Método HTTP a ser usado para solicitação de cancelamento da assinatura |
| URI do Cancelamento da Assinatura* |uri |URI do HTTP a ser usado para solicitação de cancelamento da assinatura |
| Corpo da Assinatura |corpo |Corpo da solicitação HTTP da assinatura |
| Cabeçalhos da Assinatura |headers |Cabeçalhos da solicitação HTTP da assinatura |
| Autenticação da Assinatura |Autenticação |Autenticação HTTP a ser usada para assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |
| Corpo do Cancelamento da Assinatura |corpo |Corpo da solicitação HTTP do cancelamento da assinatura |
| Cabeçalhos do Cancelamento da Assinatura |headers |Cabeçalhos da solicitação HTTP do cancelamento da assinatura |
| Autenticação do Cancelamento da Assinatura |autenticação |Autenticação HTTP a ser usada para cancelamento da assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |

**Detalhes da saída**

Solicitação de webhook

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| headers |objeto |Cabeçalhos da solicitação de webhook |
| corpo |objeto |Objeto da solicitação de webhook |
| Código de status |int |Código de status da solicitação de webhook |

## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)
* [Localizar outros conectores](apis-list.md)