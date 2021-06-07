---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "72329582"
---
Você já criou um aplicativo de funções no Azure, juntamente com a conta de armazenamento necessária. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você escreverá mensagens em uma Fila de armazenamento na mesma conta. Para se conectar à sua Conta de armazenamento ao executar a função localmente, é necessário baixar as configurações do aplicativo para o arquivo local.settings.json. 

Na raiz do projeto, execute o seguinte comando do Azure Functions Core Tools para baixar as configurações em local.settings.json, substituindo `<APP_NAME>` pelo nome do aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez você precise entrar em sua conta do Azure.

> [!IMPORTANT]  
> Esse comando substitui todas as configurações existentes por valores do aplicativo de funções no Azure.  
>
> Como ela contém segredos, o arquivo local.settings.json nunca é publicado e deve ser excluído do controle do código-fonte.  
> 

É necessário ter o valor `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Use esta conexão para verificar se a associação de saída funciona conforme o esperado.
