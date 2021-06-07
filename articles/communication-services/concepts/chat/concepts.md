---
title: Conceitos de chat nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de chat dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 292f430a1b08d59efdf05405437b3d1aa49ea2b7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168579"
---
# <a name="chat-concepts"></a>Conceitos de chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Os SDKs de Chat dos Serviços de Comunicação do Azure podem ser usados para adicionar chat por texto em tempo real aos seus aplicativos. Esta página resume os principais conceitos e funcionalidades de chat.    

Confira a [Visão geral do SDK de Chat dos Serviços de Comunicação](./sdk-features.md) para saber mais sobre as funcionalidades e as linguagens específicas do SDK.  

## <a name="chat-overview"></a>Visão geral do chat    

As conversas de chat acontecem em **threads de chat**. Os threads de chat têm as seguintes propriedades:

- Um thread de chat é identificado exclusivamente por sua `ChatThreadId`. 
- Os threads de chat podem ter como participantes um ou vários usuários que podem enviar mensagens para ele. 
- Um usuário pode fazer parte de um ou de vários threads de chat. 
- Somente os participantes do thread têm acesso a um determinado thread de chat e somente eles podem executar operações no thread de chat. Essas operações incluem enviar e receber mensagens, adicionar participantes e remover participantes. 
- Os usuários são adicionados automaticamente como participantes a qualquer thread de chat que eles criam.

### <a name="user-access"></a>Acesso do usuário
Normalmente, o criador e os participantes do thread têm o mesmo nível de acesso a ele e podem executar todas as operações relacionadas disponíveis no SDK, incluindo sua exclusão. Os participantes não têm acesso de gravação a mensagens enviadas por outros participantes, o que significa que apenas o remetente da mensagem pode atualizar ou excluir as mensagens enviadas. Se outro participante tentar fazer isso, ocorrerá um erro. 

Se quiser limitar o acesso aos recursos do chat para um conjunto de usuários, você poderá configurar o acesso como parte de seu serviço confiável. O serviço confiável é o serviço que orquestra a autenticação e a autorização dos participantes do chat. Exploraremos isso com mais detalhes abaixo.  

### <a name="chat-data"></a>Dados do chat 
Os Serviços de Comunicação armazenam o histórico do chat até que ele seja excluído explicitamente. Os participantes do thread de chat podem usar `ListMessages` para exibir o histórico de mensagens de um determinado thread. Os usuários removidos de um thread de chat poderão exibir o histórico de mensagens anteriores, mas não poderão enviar nem receber novas mensagens desse thread de chat. Um thread totalmente ocioso, sem participantes, será excluído automaticamente após 30 dias. Para saber mais sobre o armazenamento de dados pelos Serviços de Comunicação, confira a documentação sobre [privacidade](../privacy.md).  

### <a name="service-limits"></a>Limites de serviço  
- O número máximo de participantes permitidos em uma conversa de chat é 250.   
- O tamanho máximo permitido da mensagem é de aproximadamente 28 KB.  
- Para conversas de chat com mais de 20 participantes, não há suporte para os recursos de indicador de digitação e confirmações de leitura.    

## <a name="chat-architecture"></a>Arquitetura de chat    

Há duas partes principais na arquitetura de chat: 1) Serviço confiável e 2) Aplicativo cliente.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação."::: 

 - **Serviço confiável:** para gerenciar corretamente uma sessão de chat, você precisará de um serviço que ajude você a se conectar aos Serviços de Comunicação usando a cadeia de conexão do recurso. Esse serviço é responsável por criar threads de chat, adicionar e remover participantes e emitir tokens de acesso para os usuários. Encontre mais informações sobre tokens de acesso em nosso guia de início rápido sobre [tokens de acesso](../../quickstarts/access-tokens.md).  
 - **Aplicativo cliente:** o aplicativo cliente se conecta ao seu serviço confiável e recebe os tokens de acesso usados pelos usuários para se conectar diretamente aos Serviços de Comunicação. Após o serviço confiável tiver criado o thread de chat e adicionado usuários como participantes, eles poderão usar o aplicativo cliente para se conectar ao thread de chat e enviar mensagens. Use o recurso de notificações em tempo real, que abordaremos abaixo, em seu aplicativo cliente para assinar mensagens e atualizações de thread de outros participantes.
    
        
## <a name="message-types"></a>Tipos de Mensagem    

Como parte do histórico de mensagens, o Chat compartilha mensagens geradas pelo usuário e pelo sistema. As mensagens do sistema são geradas quando um thread de chat é atualizado e podem ajudar a identificar quando um participante é adicionado ou removido ou quando o tópico do thread de chat é atualizado. Quando você chama `List Messages` ou `Get Messages` em um thread de chat, o resultado contém os dois tipos de mensagem em ordem cronológica.

Para mensagens geradas pelo usuário, o tipo de mensagem pode ser definido em `SendMessageOptions` ao enviar uma mensagem ao thread de chat. Se nenhum valor for fornecido, os Serviços de Comunicação usarão o tipo `text` como padrão. Definir esse valor é importante ao enviar HTML. Quando `html` é especificado, os Serviços de Comunicação limpam o conteúdo para garantir que ele seja renderizado com segurança em dispositivos cliente.
 - `text`: uma mensagem de texto sem formatação composta e enviada por um usuário como parte de um thread de chat. 
 - `html`: uma mensagem formatada usando HTML, composta e enviada por um usuário como parte de um thread de chat. 

Tipos de mensagem do sistema: 
 - `participantAdded`: mensagem do sistema que indica que um ou mais participantes foram adicionados ao thread do chat.
 - `participantRemoved`: mensagem do sistema que indica que um participante foi removido do thread de chat.
 - `topicUpdated`: mensagem do sistema que indica que o tópico da conversa foi atualizado.

## <a name="real-time-notifications"></a>Notificações em tempo real  

Alguns SDKs (como o SDK de Chat para JavaScript) dão suporte a notificações em tempo real. Com esse recurso, os clientes podem escutar nos Serviços de Comunicação atualizações em tempo real e mensagens de entrada de um thread de chat sem precisar sondar as APIs. O aplicativo cliente pode assinar os seguintes eventos:
 - `chatMessageReceived`: quando uma nova mensagem é enviada ao thread de chat por um participante.
 - `chatMessageEdited`: quando uma mensagem é editada em uma conversa de chat. 
 - `chatMessageDeleted`: quando uma mensagem é excluída de uma conversa de chat.   
 - `typingIndicatorReceived`: quando outro participante envia um indicador de digitação ao thread de chat.    
 - `readReceiptReceived`: quando outro participante envia uma confirmação de leitura de uma mensagem que leu.  
 - `chatThreadCreated`: quando um thread de chat é criado por um usuário dos Serviços de Comunicação.    
 - `chatThreadDeleted`: quando um thread de chat é excluído por um usuário dos Serviços de Comunicação.    
 - `chatThreadPropertiesUpdated`: quando as propriedades do thread de chat são atualizadas. Atualmente, há suporte apenas para atualizar o tópico do thread. 
 - `participantsAdded`: quando um usuário é adicionado como participante do thread de chat.     
 - `participantsRemoved`: quando um participante existente é removido da conversa de chat.

Notificações em tempo real podem ser usadas para fornecer uma experiência de chat em tempo real para os usuários. Para enviar notificações por push de mensagens ignoradas pelos usuários enquanto eles estavam ausentes, os Serviços de Comunicação se integram à Grade de Eventos do Azure para publicar eventos relacionados ao chat (operação post) que podem ser conectados ao seu serviço de notificação de aplicativo personalizado. Para saber mais, confira [Eventos de servidor](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Criar experiências de chat inteligentes, da plataforma IA   

Use as [APIs Cognitivas do Azure](../../../cognitive-services/index.yml) com o SDK de Chat para criar casos de uso como:

- Permitir que os usuários conversem entre si em diferentes idiomas.  
- Ajudar um agente de suporte a priorizar tíquetes detectando um sentimento negativo de uma mensagem de entrada de um cliente. 
- Analisar as mensagens de entrada quanto à detecção de chave e o reconhecimento de entidade e solicitar informações relevantes ao usuário no seu aplicativo com base no conteúdo da mensagem.

Um modo de conseguir isso é fazendo com que o serviço confiável funcione como um participante de uma conversa de chat. Digamos que você deseje habilitar a tradução de idioma. Esse serviço será responsável por escutar as mensagens trocadas por outros participantes [1], chamar APIs cognitivas para traduzir o conteúdo para o idioma desejado [2, 3] e enviar o resultado traduzido como uma mensagem na conversa de chat [4].

Dessa forma, o histórico de mensagens conterá as mensagens originais e traduzidas. No aplicativo cliente, você poderá adicionar a lógica para mostrar a mensagem original ou traduzida. Confira [este guia de início rápido](../../../cognitive-services/translator/quickstart-translator.md) para entender como usar as APIs Cognitivas para traduzir um texto em idiomas diferentes. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagrama mostrando os Serviços Cognitivos interagindo com os Serviços de Comunicação."::: 

## <a name="next-steps"></a>Próximas etapas   

> [!div class="nextstepaction"] 
> [Introdução ao chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser do seu interesse:  
- Familiarize-se com o [SDK de Chat](sdk-features.md)
