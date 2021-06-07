---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201017"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Durante a inicialização, o host baixa e instala a [Extensão de associação de armazenamento](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) e outras extensões de associação da Microsoft. Essa instalação ocorre porque as extensões de associação estão habilitadas por padrão no arquivo *host.json* com as seguintes propriedades:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se você receber erros relacionados às extensões de associação, verifique se as propriedades acima estão presentes em *host.json*.
::: zone-end  