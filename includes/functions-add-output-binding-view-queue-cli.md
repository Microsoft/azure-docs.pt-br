---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606174"
---
Veja a fila no [portal do Azure](../articles/storage/queues/storage-quickstart-queues-portal.md) ou no [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Veja também a fila na CLI do Azure, conforme descrito nas seguintes etapas:

1. Abra o arquivo *local.setting.json* do projeto de funções e copie o valor da cadeia de conexão. Em um terminal ou uma janela Comando, execute o comando a seguir para criar uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a cadeia de conexão específica no lugar de `<MY_CONNECTION_STRING>`. (Essa variável de ambiente significa que você não precisa fornecer a cadeia de conexão para cada comando posterior usando o argumento `--connection-string`.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Opcional) Use o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para ver as filas de armazenamento em sua conta. A saída desse comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função gravou a primeira mensagem nessa fila.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Use o comando [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) para ler as mensagens dessa fila, que deve ser o primeiro nome usado ao testar a função anteriormente. O comando lê e remove a primeira mensagem da fila. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Esse script usa o Certutil para decodificar a coleção de mensagens codificadas em Base64 de um arquivo temporário local. Se não houver nenhuma saída, tente remover `> NUL` do script para deixar de suprimir a saída do Certutil, caso haja um erro. 
    
    ---
    
    Como o corpo da mensagem é armazenado [codificado em base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), a mensagem deve ser decodificada antes de ser exibida. Depois de executar `az storage message get`, a mensagem é removida da fila. Se houver apenas uma mensagem em `outqueue`, você não recuperará uma mensagem quando executar esse comando pela segunda vez e receberá um erro.
