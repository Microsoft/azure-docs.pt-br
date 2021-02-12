---
author: baanders
description: incluir arquivo para limpar uma instância dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575664"
---
* **Caso não precise de nenhum dos recursos criados neste tutorial**, você poderá excluir a instância dos Gêmeos Digitais do Azure e todos os outros recursos deste artigo com o comando [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). Isso exclui todos os recursos do Azure em um grupo de recursos, bem como o grupo de recursos em si.
    
    > [!IMPORTANT]
    > A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos.
    
    Abra o [Azure Cloud Shell](https://shell.azure.com) e execute o comando a seguir para excluir o grupo de recursos e tudo o que ele contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```