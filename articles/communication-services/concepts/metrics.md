---
title: Definições de métrica para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Este documento aborda as definições de métricas disponíveis no portal do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 129a718175fdda80d4d6852e3d3b4cea609da64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492398"
---
# <a name="metrics-overview"></a>Visão geral das métricas

Atualmente, os Serviços de Comunicação do Azure fornecem métricas para chat e SMS. O [Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) pode ser usado para plotar seus gráficos, investigar anormalidades nos valores de métricas e entender o tráfego da API usando os dados de métricas emitidos por solicitações de SMS e chat.

## <a name="where-to-find-metrics"></a>Onde encontrar métricas

Os serviços de chat e SMS dos Serviços de Comunicação do Azure emitem métricas para solicitações de API. Essas métricas podem ser encontradas na folha Métricas no recurso dos Serviços de Comunicação. Você também pode criar painéis permanentes usando a folha de pastas de trabalho no recurso dos Serviços de Comunicação.

## <a name="metric-definitions"></a>Definições de Métrica

Há dois tipos de solicitações representadas nas métricas dos Serviços de Comunicação: **Solicitações da API de chat** e **solicitações da API de SMS**.

As métricas de solicitação da API de SMS e de chat contêm três dimensões que você pode usar para filtrar os dados de métricas. Essas dimensões podem ser agregadas em conjunto usando o tipo de agregação `Count` e dão suporte a todas as séries temporais padrão de agregação do Azure, incluindo `Sum`, `Average`, `Min` e `Max`.

Encontre mais informações sobre os tipos de agregação e as agregações de série temporal compatíveis em [Recursos avançados do Azure Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **Operação**: todas as operações ou rotas que possam ser chamadas no gateway de chat do ACS.
- **Código de Status**: a resposta do código de status enviada após a solicitação.
- **StatusSubClass**: a série de códigos de status enviada após a resposta. 


### <a name="chat-api-request-metric-operations"></a>Operações de métricas de solicitação da API de chat

As seguintes operações estão disponíveis nas métricas de solicitação da API de chat:

| Operação/rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Obtém uma mensagem pela ID da mensagem. |
| ListChatMessages     | Obtém uma lista de mensagens de chat de um thread. |
| SendChatMessage      | Envia uma mensagem de chat para um thread. |
| UpdateChatMessage    | Atualiza uma mensagem de chat. |
| DeleteChatMessage    | Exclui uma mensagem de chat. |
| GetChatThread        | Obtém um thread de chat. |
| ListChatThreads      | Obtém a lista de conversas de chat de um usuário. |
| UpdateChatThread     | Atualiza as propriedades de um thread de chat. |
| CreateChatThread     | Cria um thread de chat. |
| DeleteChatThread     | Exclui um thread. |
| GetReadReceipts      | Obtém as confirmações de leitura para um thread. |
| SendReadReceipt      | Envia um evento de confirmação de leitura para um thread, em nome de um usuário. |
| SendTypingIndicator           | Posta um evento de digitação em um thread, em nome de um usuário. |
| ListChatThreadParticipants    | Obtém os membros de um thread. |
| AddChatThreadParticipants     | Adiciona membros do thread a um thread. Se os membros já existirem, nenhuma alteração ocorrerá. |
| RemoveChatThreadParticipant   | Remove um membro de um thread. |

:::image type="content" source="./media/chat-metric.png" alt-text="Métrica de solicitação da API de chat.":::

Se for feita uma solicitação para uma operação que não é reconhecida, você receberá uma resposta de valor "Rota Inválida".

### <a name="sms-api-requests"></a>Solicitações da API de SMS

As seguintes operações estão disponíveis nas métricas de solicitação da API de SMS:

| Operação/rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Envia uma mensagem SMS. |
| SMSDeliveryReportsReceived     | Obter os relatórios de entrega de SMS |
| SMSMessagesReceived      | Obtém mensagens SMS. |


:::image type="content" source="./media/sms-metric.png" alt-text="Métrica de solicitação da API de SMS.":::

### <a name="authentication-api-requests"></a>Solicitações de API de autenticação

As seguintes operações estão disponíveis nas métricas de solicitação da API de Autenticação:

| Operação/rota    | Descrição                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Cria uma identidade que representa um usuário único. |
| DeleteIdentity       | Exclui uma identidade. |
| CreateToken          | Cria um token de acesso. |
| RevokeToken          | Revoga todos os tokens de acesso criados para uma identidade antes de um tempo determinado. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Métrica de solicitação de autenticação.":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [métricas da plataforma de dados](../../azure-monitor/essentials/data-platform-metrics.md)
