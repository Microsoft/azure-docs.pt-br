---
title: Gerenciar dados de usuário com o Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como gerenciar dados de usuário com o Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83c7e808e48733487e84d668236cdb327c21c44c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688531"
---
# <a name="managing-user-data"></a>Gerenciar dados de usuário

Esta página descreve o que o serviço de nuvem Aprendiz de Conversa registra ao conduzir diálogos com os usuários finais.  Ela também descreve como associar os logs do Aprendiz de Conversa com as IDs de usuário, para que você possa recuperar ou excluir todos os logs associados a um usuário específico.

## <a name="overview-of-end-user-data-logging"></a>Visão geral do registro em log de dados do usuário final

Por padrão, o serviço de nuvem Aprendiz de Conversa registra as interações entre os usuários finais e seu bot.  Esses logs são importantes para melhorar o bot, permitindo que você identifique os casos para os quais o bot extraiu a entidade incorreta ou selecionou a ação incorreta.  Esses erros podem ser corrigidos indo até a página “Registrar diálogos em log” da interface do usuário, fazendo correções e armazenando o diálogo corrigido como um novo diálogo de treinamento. Para obter mais informações, veja o tutorial em “Registrar diálogos em log”.

## <a name="how-to-disable-logging"></a>Como desabilitar o registro em log

Você pode controlar se as conversas com os usuários finais ficam na página “Configurações” para o modelo de Aprendiz de Conversa.  Há uma caixa de seleção para “Registrar conversas em log conversas”.  Ao desmarcar esta caixa as conversas com os usuários finais não serão registradas.

## <a name="what-is-logged"></a>O que é registrado 

Na opção para registrar os diálogos em log, o Aprendiz de Conversa armazena a entrada do usuário, os valores de entidade, as ações selecionadas e os carimbos de data/hora cada vez.  Esses logs são armazenados por um período de tempo e depois são descartados (consulte a página de Ajuda em “Valor padrão e limites” para obter detalhes).  

O Aprendiz de Conversa cria uma ID exclusiva para cada diálogo registrada em log.  O Aprendiz de Conversa *não* armazena um identificador de usuário com diálogos registrados em log.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associar diálogos registrados em log com uma ID de usuário

Geralmente é importante poder associar os diálogos registrados em log com a ID do usuário – por exemplo, para que seja possível recuperar ou excluir os diálogos registrados em log de um determinado usuário.  Como o Aprendiz de Conversa não armazena um identificador de usuário, essa associação deve ser mantida pelo código do desenvolvedor.  

Para criar esse mapeamento, é necessário obter a ID da caixa de diálogo conectada em `EntityDetectionCallback`; em seguida, no armazenamento do bot, armazene a associação entre a ID de usuário e esta diálogo registrado em log.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Cabeçalhos para chamadas HTTP

Em cada uma das chamadas HTTP abaixo, adicione o cabeçalho a seguir:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

onde `<LUIS_AUTHORING_KEY>` é a chave de criação de LUIS usada para acessar seus aplicativos do Aprendiz de Conversa.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Como obter dados brutos para um diálogo registrado em log

Para obter os dados brutos para um diálogo registrado em log, você pode usar esta chamada HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Onde `<appId>` é o GUID para esse modelo de Aprendiz de Conversa e `<logDialgoId>` é a ID do diálogo registrado em log que você quer recuperar.  

> [!NOTE]
> Diálogos registrados em log podem ser editados pelo desenvolvedor e armazenadas como diálogos de treinamento.  Quando isso for feito, o Aprendiz de Conversa armazena a ID do diálogo registrado em log de “origem” com o diálogo de treinamento.  Além disso, um diálogo de treinamento pode ser “ramificado” na interface do usuário; se um diálogo de treinamento tem uma ID de diálogo registrado de origem associada, então ramificações do diálogo de treinamento serão marcadas com a mesma ID do diálogo registrado em log.

Para obter todos diálogo de treinamento derivados de um diálogo registrado em log, siga estas etapas.

Primeiramente, recupere todas os diálogos de treinamento:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Onde `<appId>` é o GUID para esse modelo de Aprendiz de Conversa.  

Isso retorna todos os diálogos de treinamento.  Pesquisar nesta lista pelo `sourceLogDialogId` associado e observe o `trainDialogId` associado. 

Para um único diálogo de treinamento por ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Onde `<appId>` é o GUID para esse modelo de Aprendiz de Conversa e `<trainDialogId>` é a ID do diálogo de treinamento que você quer recuperar.  

## <a name="how-to-delete-a-logged-dialog"></a>Como excluir um diálogo registrado em log

Se você quiser excluir um diálogo registrado em log dada a sua ID, você pode usar essa chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Onde `<appId>` é o GUID para esse modelo de Aprendiz de Conversa e `<logDialogId>` é a ID do diálogo de log que você quer excluir. 

Se você quiser excluir um diálogo de treinamento dada a sua ID, você pode usar essa chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Onde `<appId>` é o GUID para esse modelo de Aprendiz de Conversa e `<trainDialogId>` é a ID do diálogo de treinamento que você quer excluir. 
