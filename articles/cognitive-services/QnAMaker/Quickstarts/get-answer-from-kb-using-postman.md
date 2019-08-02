---
title: 'Início Rápido: usar o Postman para obter respostas da base de dados de conhecimento – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este Início Rápido fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento usando Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7c419ea7cb5c9ae1d1cc419798961ec67f250160
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563007"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Início Rápido: obter uma resposta da base de dados de conhecimento usando o Postman

Este Início Rápido baseado no Postman fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* [**Postman**](https://www.getpostman.com/) mais recente.
* Você deve ter um [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e uma [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicar para obter ponto de extremidade

Quando você estiver pronto para gerar uma resposta a uma pergunta da sua base de dados de conhecimento, [publique](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-postman"></a>Usar o ponto de extremidade de produção com o Postman

Quando sua base de dados de conhecimento for publicada, a página **Publicar** exibirá as configurações de solicitação HTTP para gerar uma resposta. A exibição padrão mostra as configurações necessárias para gerar uma resposta do [Postman](https://www.getpostman.com).

Os números em amarelo na imagem a seguir indicam quais pares nome/valor devem ser usados nas etapas a seguir.

[![Publicar os resultados](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Para gerar uma resposta com o Postman, conclua as seguintes etapas:

1. Abra o Postman. Se precisar escolher um bloco de construção, selecione o bloco de construção **Solicitação Básica**. Defina o **Nome da solicitação** como `Generate QnA Maker answer` e a **coleção** como `Generate QnA Maker answers`. Se não desejar salvá-la em uma coleção, selecione o botão **Cancelar**.
1. No workspace, selecione o método HTTP de **POST**.

    [![No Postman, defina o Método POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Para a URL, concatene o valor HOST (nº 2 na imagem) e o valor Post (nº 1 na imagem) para criar a URL completa. Uma URL de exemplo completa é semelhante a esta: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![No Postman, defina a URL completa](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selecione a guia **Cabeçalhos** sob a URL e, em seguida, selecione **Edição em massa**. 

1. Copie os cabeçalhos (nº 3 e nº 4 na imagem) para a área de texto.

    [![No Postman, defina os cabeçalhos](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selecione a guia **Corpo**.
1. Selecione o formato **bruto** e insira o JSON (nº 5 na imagem) que representa a pergunta.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![No Postman, defina o valor JSON de corpo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selecione o botão **Enviar**.
1. A resposta contém a resposta juntamente com outras informações que podem ser importantes para o aplicativo cliente. 

    [![No Postman, defina o valor JSON de corpo](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Usar o ponto de extremidade de preparo

Caso deseje obter uma resposta do ponto de extremidade de preparo, acrescente a URL com a propriedade de corpo `isTest`.

## <a name="next-steps"></a>Próximas etapas

A página de publicação também fornece informações para [gerar uma resposta](get-answer-from-kb-using-curl.md) com cURL. 

> [!div class="nextstepaction"]
> [Usar metadados ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)
