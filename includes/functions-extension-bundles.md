---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442263"
---
A maneira mais fácil de instalar as extensões de associação é habilitar [pacotes de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Com pacotes habilitados, um conjunto predefinido de extensão de pacotes é instalado automaticamente.

Para habilitar pacotes de extensão, abra o arquivo *host.json* e atualize seu conteúdo de acordo com o código a seguir:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```