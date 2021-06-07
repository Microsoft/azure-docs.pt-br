---
title: Obter modelo com chamada REST em Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ece29b7189f31654056ec357f522b59ccb16587
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95096684"
---
[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [Amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/LUIS/java-model-with-rest/Model.java)

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) ou seu IDE favorito

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Alterar o modelo de maneira programática

1. Crie uma pasta para armazenar seu projeto Java, tal como `java-model-with-rest`.

1. Crie um subdiretório chamado `lib` e copie as seguintes bibliotecas Java dentro do subdiretório `lib`:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Crie um arquivo chamado `Model.java`. Adicione os códigos a seguir:

    [!code-java[Code snippet](~/cognitive-services-quickstart-code/java/LUIS/java-model-with-rest/Model.java)]

1. Substitua os valores que começam com `YOUR-` por seus valores.

    |Informações|Finalidade|
    |--|--|
    |`YOUR-APP-ID`| Sua ID do aplicativo LUIS. |
    |`YOUR-AUTHORING-KEY`|Sua chave de criação de 32 caracteres.|
    |`YOUR-AUTHORING-ENDPOINT`| Seu ponto de extremidade da URL de criação. Por exemplo, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Você definiu o nome do recurso quando você criou o recurso.|

    As chaves e os recursos atribuídos estão visíveis no portal do LUIS na seção Gerenciar, na página **recursos do Azure**. A ID do aplicativo está disponível na mesma seção Gerenciar, na página **Configurações do Aplicativo**.

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo `Model.java`, insira o seguinte comando para compilar o arquivo Java:

    * Se você estiver usando o Windows, use este comando: `javac -cp ";lib/*" Model.java`
    * Se você estiver usando o macOS ou o Linux, use este comando: `javac -cp ":lib/*" Model.java`

1. Execute o aplicativo Java da linha de comando inserindo o seguinte texto no prompt de comando:

    * Se você estiver usando o Windows, use este comando: `java -cp ";lib/*" Model`
    * Se você estiver usando o macOS ou o Linux, use este comando: `java -cp ":lib/*" Model`

1. Examine a resposta de criação:

    ```console
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    {"statusId":9,"status":"Queued"}
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":3,"status":"InProgress","exampleCount":0,"progressSubstatus":"CollectingData"}}]
    ```

    Esta é a saída formatada para facilitar a leitura:

    ```json
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    {
      "statusId": 9,
      "status": "Queued"
    }
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 3,
          "status": "InProgress",
          "exampleCount": 0,
          "progressSubstatus": "CollectingData"
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir este início rápido, exclua a pasta do projeto do sistema de arquivos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)