---
title: Visão geral do SDK de chat para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o SDK de chat dos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500678"
---
# <a name="chat-sdk-overview"></a>Visão geral do SDK de chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Os SDKs de chat dos Serviços de Comunicação do Azure podem ser usados para adicionar um chat sofisticado e em tempo real aos seus aplicativos.
    
## <a name="chat-sdk-capabilities"></a>Funcionalidades do SDK de chat    

A lista a seguir apresenta o conjunto de recursos que estão atualmente disponíveis nos SDKs de chat dos Serviços de Comunicação.  

| Grupo de recursos | Funcionalidade | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Principais funcionalidades | Criar uma conversa de chat entre dois ou mais usuários                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Atualizar o tópico de uma conversa de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Adicionar ou remover participantes de uma conversa de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Escolher se deseja compartilhar o histórico de mensagens de chat com o participante que está sendo adicionado                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obter uma lista de participantes em uma conversa de chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Excluir uma conversa de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dado um usuário de comunicação, obtenha a lista de conversas de chat do qual o usuário faz parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obter informações de uma conversa de chat específica                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Enviar e receber mensagens em uma conversa de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Atualizar o conteúdo da mensagem enviada                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Excluir uma mensagem que você enviou anteriormente                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Confirmações de leitura de mensagens que foram lidas por outros participantes em um chat                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Receber notificação quando os participantes estiverem digitando ativamente uma mensagem em uma conversa de chat                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Obter todas as mensagens de uma conversa de chat                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Enviar emojis Unicode como parte do conteúdo da mensagem                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Notificações em tempo real (habilitadas pelo pacote de sinalização do proprietário**)|  Os clientes de chat podem inscrever-se para obter atualizações em tempo real de mensagens de entrada e de outras operações que ocorrem em uma conversa de chat. Para ver uma lista de atualizações com suporte para notificações em tempo real, confira [Conceitos de chat](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integração com a Grade de Eventos do Azure             | Use os eventos de chat disponíveis na Grade de Eventos do Azure para conectar os serviços de notificação personalizados ou postar o evento em um webhook para executar uma lógica de negócios como a atualização de registros do CRM após a conclusão de um chat   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Relatórios </br>(Essas informações estão disponíveis na guia Monitoramento do recurso Serviços de Comunicação no portal do Azure)      | Entenda o tráfego de API do seu aplicativo de chat monitorando as métricas publicadas no Metrics Explorer do Azure e defina alertas para detectar anormalidades     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorar e depurar a solução de Serviços de Comunicação habilitando o log de diagnósticos para o recurso    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**O pacote de sinalização proprietário é implementado usando soquetes Web. Ele fará o fallback para a sondagem longa se não houver suporte para soquetes Web.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Suporte ao SDK de chat do JavaScript por sistema operacional e navegador    

A tabela a seguir representa o conjunto de navegadores e versões com suporte disponíveis no momento.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operacional do iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **SDK de chat** | Firefox *, Chrome*, novo Microsoft Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Observe há suporte para a última versão, além das duas versões anteriores.<br/>   

## <a name="next-steps"></a>Próximas etapas   

> [!div class="nextstepaction"] 
> [Introdução ao chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser do seu interesse:  
- Familiarize-se com os [conceitos de chat](../chat/concepts.md)
- Entenda como o [preço](../pricing.md#chat) funciona para o chat
