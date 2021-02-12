---
title: Armazenamento de dados-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS armazena dados criptografados em um armazenamento de dados do Azure correspondente à região especificada pela chave.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008445"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento e remoção de dados nos Serviços Cognitivos do LUIS (Reconhecimento vocal)

O LUIS armazena dados criptografados em um repositório de dados do Azure correspondente à [região](luis-reference-regions.md) especificada pela chave. 

* Os dados usados para treinar o modelo, como entidades, intenções e declarações, serão salvos em LUIS durante o tempo de vida do aplicativo. Se um proprietário ou colaborador excluir o aplicativo, esses dados serão excluídos com ele. Se um aplicativo não tiver sido usado em 90 dias, ele será excluído. 

* Os autores de aplicativos podem optar por [habilitar o registro em log](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) no declarações que são enviados para um aplicativo publicado. Se habilitado, o declarações será salvo por 30 dias e poderá ser exibido pelo autor do aplicativo. Se o registro em log não estiver habilitado quando o aplicativo for publicado, esses dados não serão armazenados.

## <a name="export-and-delete-app"></a>Exportar e excluir o aplicativo
Os usuários têm controle completo sobre a [exportação](luis-how-to-start-new-app.md#export-app) e [exclusão](luis-how-to-start-new-app.md#delete-app) do aplicativo. 

## <a name="utterances"></a>Declarações

Declarações pode ser armazenado em dois locais diferentes. 

* Durante **o processo de criação**, os declarações são criados e armazenados na intenção. Declarações em tentativas são necessárias para um aplicativo LUIS bem-sucedido. Depois que o aplicativo é publicado e recebe consultas no ponto de extremidade, o QueryString da solicitação do ponto de extremidade, `log=false` determina se o ponto de extremidade expressão está armazenado. Se o ponto de extremidade estiver armazenado, ele se tornará parte do declarações de aprendizado ativo encontrado na seção **Build** do portal, na seção **Review Endpoint declarações** . 
* Quando você **examina o ponto de extremidade declarações** e adiciona um expressão a uma intenção, o expressão não é mais armazenado como parte do ponto de extremidade declarações a ser revisado. Ele é adicionado às intenções do aplicativo. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Excluir declarações de exemplo de uma intenção

Exclua declarações de exemplo usadas para treinar o [LUIS](luis-reference-regions.md). Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Excluir declarações em análise do aprendizado ativo

É possível excluir declarações da lista de declarações do usuário sugeridas pelo LUIS na **[página Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)**. Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.

Se não quiser o declarações de aprendizado ativo, você poderá [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Desabilitar o aprendizado ativo também desabilita o registro em log.

### <a name="disable-logging-utterances"></a>Desabilitar declarações de registro em log
[Desabilitar o aprendizado ativo desabilita](luis-how-to-review-endpoint-utterances.md#disable-active-learning) o registro em log.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Excluir uma conta
Se você não estiver migrado, poderá excluir sua conta e todos os seus aplicativos serão excluídos junto com seus declarações de exemplo e logs. Os dados são retidos por 90 dias antes que a conta e os dados sejam excluídos permanentemente.

A exclusão da conta está disponível na página **Configurações**. Selecione o nome da sua conta na barra de navegação superior direita para chegar à página **Configurações**.

## <a name="delete-an-authoring-resource"></a>Excluir um recurso de criação
Se você tiver [migrado para um recurso de criação](./luis-migration-authoring.md), a exclusão do próprio recurso do portal do Azure excluirá todos os aplicativos associados a esse recurso, juntamente com seus declarações de exemplo e logs. Os dados são retidos por 90 dias antes de serem excluídos permanentemente.    

Para excluir o recurso, vá para a [portal do Azure](https://ms.portal.azure.com/#home) e selecione o recurso de criação do Luis. Vá para a guia **visão geral** e clique no botão **excluir** na parte superior da página. Em seguida, confirme se o recurso foi excluído. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como uma assinatura expirada
Para os fins da retenção e da exclusão de dados, um aplicativo LUIS inativo pode, _a critério da Microsoft_, ser tratado como uma assinatura expirada. Um aplicativo será considerado inativo se ele atender aos seguintes critérios para os últimos 90 dias: 

* **Não** teve chamadas feitas a ele.
* Não foi modificado.
* Não tem uma chave atual atribuída a ele.
* Não tem uma credencial de usuário para ele.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre como exportar e excluir um aplicativo](luis-how-to-start-new-app.md)