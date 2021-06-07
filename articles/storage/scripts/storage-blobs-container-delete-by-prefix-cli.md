---
title: Amostra de Script da CLI do Azure – excluir contêineres por prefixo | Microsoft Docs
description: Exclua contêineres de blob de armazenamento do Azure com base em um prefixo de nome de contêiner e, em seguida, limpe a implantação. Confira os links de ajuda para comandos usados no exemplo de script.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87901871"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Usar um script da CLI do Azure para excluir contêineres com base no prefixo de nome de contêiner

Esse script primeiro cria alguns contêineres de amostra no Armazenamento de Blobs do Azure e, em seguida, exclui alguns dos contêineres com base em um prefixo no nome do contêiner.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, os contêineres restantes e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir contêineres com base no prefixo do nome do contêiner. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account) | Cria uma conta de Armazenamento do Azure no grupo de recursos especificado. |
| [az storage container create](/cli/azure/storage/container) | Cria um contêiner no Armazenamento de Blobs do Azure. |
| [az storage container list](/cli/azure/storage/container) | Lista os contêineres em uma conta de Armazenamento do Azure. |
| [az storage container delete](/cli/azure/storage/container) | Exclui os contêineres em uma conta de Armazenamento do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Amostras adicionais de script CLI de armazenamento podem ser encontradas nas [Amostras de CLI do Azure para Armazenamento do Azure](../blobs/storage-samples-blobs-cli.md).
