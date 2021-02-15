---
title: 'Início Rápido: Obter respostas da base de dados de conhecimento – REST, Java – QnA Maker'
description: Este início rápido baseado em Java REST orienta você para obter uma resposta de uma base de dados de conhecimento programaticamente.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 8075371bddfc92d7477b3b036575836aa65f1cd0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346024"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento com Java

Este início rápido o orienta a obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes de dados](../index.yml), como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta prevista na parte superior.

[Documentação de referência](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support) (Java Development Kit, Standard Edition)
* Esse exemplo usa o [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) do Apache de componentes HTTP. Você precisa adicionar as bibliotecas de cliente Apache HTTP a seguir ao seu projeto:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Chaves** em **Gerenciamento de Recursos** no painel do Azure para seu recurso do QnA Maker.
* Configurações da página **Publicar**. Caso não tenha uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe-a na página **Configurações** e depois publique-a. Você pode baixar e usar [essa base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    As configurações da página de publicação incluem o valor da rota, o valor do Host e o valor do EndpointKey.

    ![Configurações de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Criar um arquivo Java

Abra o VSCode, crie um novo arquivo chamado `GetAnswer.java` e adicione a seguinte classe:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Este início rápido usa classes do Apache para solicitações HTTP. Acima da classe GetAnswer, na parte superior do arquivo `GetAnswer.java`, adicione as dependências necessárias ao projeto:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe `GetAnswer.java`, adicione as constantes necessárias para acessar o QnA Maker. Esses valores ficam na página **Publicar** após a publicação da base de dados de conhecimento.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="constants":::

## <a name="add-a-post-request-to-send-question"></a>Adicionar uma solicitação POST para enviar perguntas

O código a seguir faz uma solicitação HTTPS à API de QnA Maker para enviar a pergunta à base de dados de conhecimento e recebe a resposta:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="post":::

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey`.

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compilar e executar o programa da linha de comando. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

1. Compile o arquivo:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Execute o arquivo:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)