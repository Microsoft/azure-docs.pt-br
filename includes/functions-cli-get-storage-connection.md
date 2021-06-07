---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673342"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Recuperar a cadeia de conexão do Armazenamento do Azure

Anteriormente, você criou uma conta de Armazenamento do Azure para uso pelo aplicativo de funções. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Ao baixar a configuração para o arquivo *local.settings.json*, use essa conexão para gravar em uma fila do Armazenamento na mesma conta ao executar a função localmente. 

1. Na raiz do projeto, execute o comando a seguir, substituindo `<app_name>` pelo nome do aplicativo de funções do início rápido anterior. Esse comando substituirá todos os valores existentes no arquivo.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor chamado `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Você usará o nome `AzureWebJobsStorage` e a cadeia de conexão em outras seções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json* contém segredos baixados do Azure, sempre exclua esse arquivo do controle do código-fonte. O arquivo *.gitignore* criado com um projeto de funções locais exclui o arquivo por padrão.