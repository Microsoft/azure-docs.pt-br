---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78262226"
---
### <a name="set-the-storage-account-connection"></a>Definir a conexão da Conta de armazenamento

Abra o arquivo local.settings.json e copie o valor de `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` como a cadeia de conexão usando o seguinte comando do Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Quando você definir a cadeia de conexão na variável de ambiente `AZURE_STORAGE_CONNECTION_STRING`, é possível acessar a conta de Armazenamento sem ter que fornecer a autenticação toda vez.