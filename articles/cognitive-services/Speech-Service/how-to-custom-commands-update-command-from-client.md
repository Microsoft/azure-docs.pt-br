---
title: Atualizar um comando de um aplicativo cliente
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar um comando de um aplicativo cliente.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560280"
---
# <a name="update-a-command-from-a-client-app"></a>Atualizar um comando de um aplicativo cliente

Neste artigo, você aprenderá a atualizar um comando contínuo de um aplicativo cliente.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente

## <a name="update-the-state-of-a-command"></a>Atualizar o estado de um comando

Se o aplicativo cliente exigir que você atualize o estado de um comando contínuo sem entrada de voz, você poderá enviar um evento para atualizar o comando.

Para ilustrar esse cenário, envie a seguinte atividade de evento para atualizar o estado de um comando contínuo ( `TurnOnOff` ): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Vamos examinar os principais atributos desta atividade:

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **tipo** | A atividade é do tipo `"event"` e o nome do evento precisa ser `"RemoteUpdate"` . |
| **value** | O atributo `"value"` contém os atributos necessários para atualizar o comando atual. |
| **updatedCommand** | O atributo `"updatedCommand"` contém o nome do comando. Dentro desse atributo, `"updatedParameters"` é um mapa com os nomes dos parâmetros e seus valores atualizados. |
| **cancel** | Se o comando contínuo precisar ser cancelado, defina o atributo `"cancel"` como `true` . |
| **updatedGlobalParameters** | O atributo `"updatedGlobalParameters"` é um mapa, assim como `"updatedParameters"` , mas é usado para parâmetros globais. |
| **processTurn** | Se a desativação precisar ser processada depois que a atividade for enviada, defina o atributo `"processTurn"` como `true` . |

Você pode testar esse cenário no portal de comandos personalizados:

1. Abra o aplicativo comandos personalizados que você criou anteriormente. 
1. Selecione **treinar** e **testar**.
1. Enviar `turn` .
1. Abra o painel lateral e selecione **Editor de atividade**.
1. Digite e envie o `RemoteCommand` evento especificado na seção anterior.
    > [!div class="mx-imgBorder"]
    > ![Captura de tela que mostra o evento para um comando remoto.](media/custom-commands/send-remote-command-activity.png)

Observe como o valor do parâmetro `"OnOff"` foi definido como `"on"` por meio de uma atividade do cliente em vez de fala ou texto.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Atualizar o catálogo do parâmetro para um comando

Quando você configura a lista de opções válidas para um parâmetro, os valores para o parâmetro são definidos globalmente para o aplicativo. 

Em nosso exemplo, o `SubjectDevice` parâmetro terá uma lista fixa de valores com suporte, independentemente da conversa.

Se você quiser adicionar novas entradas ao catálogo do parâmetro por conversa, poderá enviar a seguinte atividade:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Com essa atividade, você adicionou uma entrada para `"stereo"` ao catálogo do parâmetro `"SubjectDevice"` no comando `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Captura de tela que mostra uma atualização de catálogo.":::

Observe algumas coisas:
- Você precisa enviar essa atividade apenas uma vez (ideal, logo depois de iniciar uma conexão).
- Depois de enviar essa atividade, você deve aguardar até que o evento `ParameterCatalogsUpdated` seja enviado de volta ao cliente.

## <a name="add-more-context-from-the-client-application"></a>Adicionar mais contexto do aplicativo cliente

Você pode definir contexto adicional do aplicativo cliente por conversa que pode ser usado posteriormente em seu aplicativo de comandos personalizados. 

Por exemplo, pense no cenário em que você deseja enviar a ID e o nome do dispositivo conectado ao aplicativo de comandos personalizados.

Para testar esse cenário, vamos criar um novo comando no aplicativo atual:
1. Crie um novo comando chamado `GetDeviceInfo` .
1. Adicione uma frase de exemplo de `get device info` .
1. Na regra de conclusão **concluída**, adicione uma ação **Enviar resposta de fala** que contém os atributos de `clientContext` .
   ![Captura de tela que mostra uma resposta para enviar fala com contexto.](media/custom-commands/send-speech-response-context.png)
1. Salve, treine e teste seu aplicativo.
1. Na janela de teste, envie uma atividade para atualizar o contexto do cliente.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Envie o texto `get device info` .
   ![Captura de tela que mostra uma atividade para enviar o contexto do cliente.](media/custom-commands/send-client-context-activity.png)

Observe algumas coisas:
- Você precisa enviar essa atividade apenas uma vez (ideal, logo depois de iniciar uma conexão).
- Você pode usar objetos complexos para o `clientContext` .
- Você pode usar `clientContext` em respostas de fala para enviar atividades e para chamar pontos de extremidade da Web.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atualizar um comando de um ponto de extremidade da Web](./how-to-custom-commands-update-command-from-web-endpoint.md)
