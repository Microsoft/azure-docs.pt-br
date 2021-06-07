---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94340968"
---
Atributos de associação são definidos diretamente no arquivo function.json. Dependendo do tipo de associação, outras propriedades podem ser necessárias. A [configuração de saída da fila](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) descreve os campos obrigatórios para uma associação de fila do Armazenamento do Azure. A extensão facilita a inclusão de associações no arquivo function.json. 

Para criar uma associação, clique com o botão direito do mouse (Ctrl+clique no macOS) no arquivo `function.json` na pasta HttpTrigger e escolha **Adicionar associação...** . Siga os prompts para definir as seguintes propriedades de associação para a nova associação:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção...** | `Azure Queue Storage` | A associação é uma associação de fila do Armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila à qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **Selecione a configuração de "local.setting.json"** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão da Conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a Conta de armazenamento criada com o aplicativo de funções. |

Uma associação é incluída na matriz `bindings` no seu function.json, que deve ter a seguinte aparência:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
