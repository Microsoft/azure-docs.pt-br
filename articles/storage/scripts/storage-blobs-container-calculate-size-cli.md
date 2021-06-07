---
title: Amostra de script da CLI do Azure – calcular o tamanho do contêiner de blob | Microsoft Docs
description: Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 45712632ebfb2da4b713038503965ce908c1dfc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87498881"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contêiner de Armazenamento de Blobs

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do CLI fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança.

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, o contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para calcular o tamanho do contêiner de Armazenamento de Blobs. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage blob upload](/cli/azure/storage/account) | Carrega arquivos locais para um contêiner de Armazenamento de Blobs do Azure. |
| [az storage blob list](/cli/azure/storage/account/keys) | Lista os blobs em um contêiner de Armazenamento de Blobs do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Amostras adicionais de script CLI de armazenamento podem ser encontradas nas [Amostras de CLI do Azure para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-cli.md).
