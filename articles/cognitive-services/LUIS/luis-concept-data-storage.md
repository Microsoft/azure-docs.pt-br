---
title: Armazenamento de dados-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639229"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento e remoção de dados nos Serviços Cognitivos do LUIS (Reconhecimento vocal)
O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave. Esses dados são armazenados por 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e excluir o aplicativo
Os usuários têm controle completo sobre a [exportação](luis-how-to-start-new-app.md#export-app) e [exclusão](luis-how-to-start-new-app.md#delete-app) do aplicativo. 

## <a name="utterances"></a>Declarações

Declarações pode ser armazenado em dois locais diferentes. 

* Durante **o processo de criação**, os declarações são criados e armazenados na intenção. Declarações em tentativas são necessárias para um aplicativo LUIS bem-sucedido. Depois que o aplicativo é publicado e recebe consultas no ponto de extremidade, o QueryString da solicitação `log=false`do ponto de extremidade, determina se o ponto de extremidade expressão está armazenado. Se o ponto de extremidade estiver armazenado, ele se tornará parte do declarações de aprendizado ativo encontrado na seção **Build** do portal, na seção **Review Endpoint declarações** . 
* Quando você **examina o ponto de extremidade declarações**e adiciona um expressão a uma intenção, o expressão não é mais armazenado como parte do ponto de extremidade declarações a ser revisado. Ele é adicionado às intenções do aplicativo. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Excluir declarações de exemplo de uma intenção

Exclua enunciados de exemplo usados para treinar o [LUIS](luis-reference-regions.md). Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Excluir declarações em análise do aprendizado ativo

É possível excluir declarações da lista de declarações do usuário sugeridas pelo LUIS na **[página Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)** . Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.

Se não quiser o declarações de aprendizado ativo, você poderá [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Desabilitar o aprendizado ativo também desabilita o registro em log.

### <a name="disable-logging-utterances"></a>Desabilitar declarações de registro em log
[Desabilitar o aprendizado ativo desabilita](luis-how-to-review-endpoint-utterances.md#disable-active-learning) o registro em log.


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
