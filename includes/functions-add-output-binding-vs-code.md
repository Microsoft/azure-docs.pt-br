---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493931"
---
Visual Studio Code permite que você adicione associações ao seu function.jsno arquivo seguindo um conjunto conveniente de prompts. 

Para adicionar uma associação, abra o palete de comando (F1) e digite **Azure Functions: Adicionar associação...**, escolha a função para a nova associação e, em seguida, siga os prompts, que variam dependendo do tipo de associação que está sendo adicionada à função. 

A seguir estão exemplos de prompts para definir uma nova associação de saída de armazenamento:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção** | `Azure Queue Storage` | A associação é uma associação de fila do Armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila à qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **Selecione configuração em "local.settings.js"** | `MyStorageConnection` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A `AzureWebJobsStorage` configuração contém a cadeia de conexão para a conta de armazenamento que você criou com o aplicativo de funções. |

Você também pode clicar com o botão direito do mouse (Ctrl + clique no macOS) diretamente na **function.jsno** arquivo em sua pasta de funções, selecione **Adicionar Associação** e siga os mesmos prompts.

Neste exemplo, a seguinte associação é adicionada à `bindings` matriz em seu function.jsno arquivo:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```