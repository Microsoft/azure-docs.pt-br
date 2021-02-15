---
title: Visão geral da biblioteca de clientes de chat dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre a biblioteca de clientes de chat dos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b239cf6d253c1c2d2e36d213e92e0b218add3f8c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94885981"
---
# <a name="chat-client-library-overview"></a>Visão geral da biblioteca de clientes de chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

As bibliotecas de clientes de chat dos Serviços de Comunicação do Azure podem ser usadas para adicionar um chat sofisticado e em tempo real aos seus aplicativos.

## <a name="chat-client-library-capabilities"></a>Funcionalidades da biblioteca de clientes de chat

A lista a seguir apresenta o conjunto de recursos que estão atualmente disponíveis nas bibliotecas de clientes de chat dos Serviços de Comunicação.

| Grupo de recursos | Funcionalidade                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Principais funcionalidades | Criar uma conversa de chat entre dois ou mais usuários (até 250 usuários)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Atualizar o tópico de uma conversa de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Adicionar ou remover membros de uma conversa de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Escolher se deseja compartilhar o histórico de mensagens de chat com os membros recém-adicionados: *todos/nenhum/até certo momento* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Obter uma lista de todas as conversas de membros do chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Excluir uma conversa de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obter uma lista de associações de conversa de chat de um usuário                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obter informações de uma conversa de chat específica                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Enviar e receber mensagens em uma conversa de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Editar o conteúdo de uma mensagem após o envio                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Excluir uma mensagem                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Marcar uma mensagem com prioridade como normal ou alta no momento do envio                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Enviar e receber confirmações de leitura das mensagens lidas pelos membros <br/> *Não disponível quando há mais de 20 membros em uma conversa de chat*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Enviar e receber notificações de digitação quando um membro estiver digitando ativamente uma mensagem em uma conversa de chat <br/> *Não disponível quando há mais de 20 membros em uma conversa de chat*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Obter todas as mensagens de uma conversa de chat <br/> *Emojis Unicode com suporte*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Enviar emojis como parte do conteúdo da mensagem                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Sinalização em tempo real (habilitada pelo pacote de sinalização proprietário)| Ser notificado quando um usuário receber uma nova mensagem em uma conversa de chat do qual ele é membro                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Ser notificado quando uma mensagem for editada por outro membro em uma conversa de chat do qual ele é membro                | ✔️   | ❌    | ❌    | ❌  |
|                    | Ser notificado quando uma mensagem for excluída por outro membro em uma conversa de chat do qual ele é membro                | ✔️   | ❌    | ❌    | ❌  |
|                    | Ser notificado quando outro membro da conversa de chat estiver digitando                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Ser notificado quando outro membro ler uma mensagem (confirmação de leitura) na conversa de chat                               | ✔️   | ❌    | ❌    | ❌  |
| Eventos             | Usar a Grade de Eventos para assinar a atividade do usuário que ocorre em conversas de chat e integrar os serviços de notificação personalizados ou a lógica de negócios     | ✔️   | ✔️  | ✔️    | ✔️  |
| Monitoramento        | Monitorar o uso em termos de mensagens enviadas                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Monitorar a qualidade e o status das solicitações de API feitas pelo aplicativo e configurar alertas por meio do portal                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Recursos adicionais | Usar a [API de Serviços Cognitivos](../../../cognitive-services/index.yml) junto com a biblioteca de clientes de chat para habilitar recursos inteligentes: *tradução de idioma e análise de sentimento da mensagem de entrada em um cliente, conversão de fala em texto para compor uma mensagem enquanto o membro fala etc.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao chat](../../quickstarts/chat/get-started.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com os [conceitos de chat](../chat/concepts.md)