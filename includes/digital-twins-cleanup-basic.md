---
author: baanders
description: incluir arquivo para limpar uma instância dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799992"
---
* **Caso não precise de nenhum dos recursos criados neste tutorial**, você poderá excluir a instância dos Gêmeos Digitais do Azure e todos os outros recursos deste artigo com o comando [az group delete](/cli/azure/group#az_group_delete). Isso exclui todos os recursos do Azure em um grupo de recursos, bem como o grupo de recursos em si.
    
    > [!IMPORTANT]
    > A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos.
    
    Abra o [Azure Cloud Shell](https://shell.azure.com) e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```