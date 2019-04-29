---
title: Armazenamento de dados
titleSuffix: Language Understanding - Azure Cognitive Services
description: O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812946"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento e remoção de dados nos Serviços Cognitivos do LUIS (Reconhecimento vocal)
O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave. Esses dados são armazenados por 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e excluir o aplicativo
Os usuários têm controle completo sobre a [exportação](luis-how-to-start-new-app.md#export-app) e [exclusão](luis-how-to-start-new-app.md#delete-app) do aplicativo. 

## <a name="utterances"></a>Declarações

Declarações podem ser armazenadas em dois locais diferentes. 

* Durante **o processo de criação**, declarações são criadas e armazenadas na intenção. Declarações em intenções são necessárias para um aplicativo LUIS com êxito. Depois que o aplicativo é publicado e recebe consultas no ponto de extremidade, querystring da solicitação de ponto de extremidade, `log=false`, determina se a expressão de ponto de extremidade será armazenado. Se o ponto de extremidade é armazenado, ele se torna parte das declarações de aprendizado ativo encontrado na **Build** seção do portal, no **examine as declarações de ponto de extremidade** seção. 
* Quando você **examine as declarações de ponto de extremidade**e adicionar uma expressão a uma intenção, a expressão não é armazenado como parte de declarações de ponto de extremidade a ser revisado. Ele é adicionado para propósitos do aplicativo. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Excluir as declarações de exemplo de uma intenção
Exclua enunciados de exemplo usados para treinar o [LUIS](luis-reference-regions.md). Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Excluir declarações de revisão de aprendizado ativo

É possível excluir declarações da lista de declarações do usuário sugeridas pelo LUIS na **[página Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)**. Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.

Se você não quiser que as declarações de aprendizado ativo, você poderá [desabilitar aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Desabilitar o aprendizado ativo também desabilita o registro em log.

### <a name="disable-logging-utterances"></a>Desabilitar o registro em log declarações
[Desabilitando o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning) é desabilita o registro em log.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Excluir uma conta
Se você excluir uma conta, todos os aplicativos serão excluídos, junto com os respectivos logs e enunciados de exemplo. Os dados são mantidos por 60 dias antes da exclusão permanente da conta e dos dados.

A exclusão da conta está disponível na página **Configurações**. Selecione o nome da sua conta na barra de navegação superior direita para chegar à página **Configurações**.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como uma assinatura expirada
Para os fins da retenção e da exclusão de dados, um aplicativo LUIS inativo pode, _a critério da Microsoft_, ser tratado como uma assinatura expirada. Um aplicativo será considerado inativo se ele atender aos seguintes critérios para os últimos 90 dias: 

* **Não** teve chamadas feitas a ele.
* Não foi modificado.
* Não tem uma chave atual atribuída a ele.
* Não tem uma credencial de usuário para ele.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre como exportar e excluir um aplicativo](luis-how-to-start-new-app.md)
