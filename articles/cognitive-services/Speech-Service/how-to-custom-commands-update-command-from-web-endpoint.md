---
title: Atualizar um comando de um ponto de extremidade da Web
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar o estado de um comando usando uma chamada para um ponto de extremidade da Web.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560263"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Atualizar um comando de um ponto de extremidade da Web

Se o aplicativo cliente exigir uma atualização para o estado de um comando contínuo sem entrada de voz, você poderá usar uma chamada para um ponto de extremidade da Web para atualizar o comando.

Neste artigo, você aprenderá a atualizar um comando contínuo de um ponto de extremidade da Web.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente

## <a name="create-an-azure-function"></a>Criar uma função do Azure 

Para este exemplo, você precisará de uma [função do Azure](../../azure-functions/index.yml) disparada por http que dá suporte à seguinte entrada (ou a um subconjunto dessa entrada):

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Vamos examinar os principais atributos desta entrada:

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | O identificador exclusivo da conversa. Observe que essa ID pode ser gerada no aplicativo cliente. |
| **currentCommand** | O comando que está ativo no momento na conversa. |
| **name** | O nome do comando. O `parameters` atributo é um mapa com os valores atuais dos parâmetros. |
| **currentGlobalParameters** | Um mapa como `parameters` , mas usado para parâmetros globais. |

A saída da função do Azure precisa oferecer suporte ao seguinte formato:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Você pode reconhecer esse formato porque ele é o mesmo que você usou ao [atualizar um comando do cliente](./how-to-custom-commands-update-command-from-client.md). 

Agora, crie uma função do Azure com base em Node.js. Copiar/colar este código:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Ao chamar essa função do Azure de comandos personalizados, você enviará os valores atuais da conversa. Você retornará os parâmetros que deseja atualizar ou se quiser cancelar o comando atual.

## <a name="update-the-existing-custom-commands-app"></a>Atualizar o aplicativo de comandos personalizados existente

Vamos conectar a função do Azure com o aplicativo de comandos personalizados existente:

1. Adicione um novo comando chamado `IncrementCounter` .
1. Adicione apenas uma frase de exemplo com o valor `increment` .
1. Adicione um novo parâmetro chamado `Counter` (mesmo nome especificado na função do Azure) do tipo `Number` com um valor padrão de `0` .
1. Adicione um novo ponto de extremidade da Web chamado `IncrementEndpoint` com a URL de sua função do Azure, com **as atualizações remotas** definidas como **habilitado**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Captura de tela que mostra a configuração de um ponto de extremidade da Web com atualizações remotas.":::
1. Crie uma nova regra de interação chamada **IncrementRule** e adicione uma ação **chamar ponto de extremidade da Web** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Captura de tela que mostra a criação de uma regra de interação.":::
1. Na configuração da ação, selecione `IncrementEndpoint` . Configure **em** caso de sucesso para **Enviar resposta de fala** com o valor de `Counter` e configurar **em caso de falha** com uma mensagem de erro.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Captura de tela que mostra como definir um contador de incremento para chamar um ponto de extremidade da Web.":::
1. Defina o estado de pós-execução da regra para **aguardar a entrada do usuário**.

## <a name="test-it"></a>Testá-lo

1. Salve e treine seu aplicativo.
1. Selecione **Testar**.
1. Envie `increment` algumas vezes (que é a frase de exemplo para o `IncrementCounter` comando).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Captura de tela que mostra um exemplo de contador de incremento.":::

Observe como a função do Azure incrementa o valor do `Counter` parâmetro em cada rodada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar um processo de CI/CD para o aplicativo de Comandos Personalizados](./how-to-custom-commands-deploy-cicd.md)