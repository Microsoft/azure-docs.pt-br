---
title: "Amostra de script da CLI do Azure – reciclar chaves de acesso da conta de armazenamento | Microsoft Docs"
description: Crie uma conta de Armazenamento do Azure e, em seguida, recupere e recicle as respectivas chaves de acesso da conta.
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: marsma
ms.openlocfilehash: 1b59144e0426b50c2ac49acbd7914d975ff037ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Criar uma conta de armazenamento e reciclar suas chaves de acesso da conta

Esse script cria uma conta de Armazenamento do Azure, exibe as chaves de acesso da nova conta de armazenamento e, em seguida, renova (recicla) as chaves.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar a conta de armazenamento e recuperar e reciclar as respectivas chaves de acesso. Cada item em que a tabela contém links para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#create) | Cria uma conta de Armazenamento do Azure no grupo de recursos especificado. |
| [az storage account keys list](/cli/azure/storage/account/keys#list) | Exibe as chaves de acesso da conta de armazenamento da conta especificada. |
| [az storage account keys renew](/cli/azure/storage/account/keys#renew) | Regenera a chave de acesso da conta de armazenamento primária ou secundária. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure/overview).

Amostras adicionais de script CLI de armazenamento podem ser encontradas nas [Amostras de CLI do Azure para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-cli.md).
