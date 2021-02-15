---
author: baanders
description: incluir o arquivo dos Gêmeos Digitais do Azure – configurar a extensão do IoT mais recente
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606805"
---
Primeiro, execute este comando para ver uma lista de todas as extensões que você já instalou.

```azurecli
az extension list
```

A saída é uma matriz de todas as extensões que você tem atualmente. Procure o campo `"name"` para cada entrada de lista para ver os nomes das extensões.

Use a saída para determinar quais dos comandos a seguir devem ser executados para a configuração da extensão (você pode executar mais de um).
* Se a lista contiver `azure-iot`: Você já tem a extensão. Execute este comando para verificar se você tem a atualização mais recente e se não há mais atualizações disponíveis:

   ```azurecli
   az extension update --name azure-iot
   ```

* Se a lista **não** contém `azure-iot`: Você precisa instalar a extensão. Use este comando:

    ```azurecli
    az extension add --name azure-iot
    ```

* Se a lista contiver `azure-iot-cli-ext`: Essa é a versão herdada da extensão. Somente uma versão da extensão deve ser instalada de cada vez, portanto, você deve desinstalar a extensão herdada. Use este comando:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```