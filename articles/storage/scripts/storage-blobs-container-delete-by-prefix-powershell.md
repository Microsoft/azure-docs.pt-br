---
title: Amostra de Script do Azure PowerShell – excluir contêineres por prefixo | Microsoft Docs
description: Leia um exemplo que mostra como excluir o armazenamento de Blobs do Azure com base em um prefixo no nome do contêiner usando o Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b95c5ab243fbd938e8a7eb1d3b9619b0d46fb046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89072938"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Excluir contêineres com base no prefixo de nome de contêiner

Este script exclui os contêineres no Armazenamento de Blobs do Azure com base em um prefixo no nome do contêiner.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, os contêineres restantes e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir contêineres com base no prefixo do nome do contêiner. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento em um grupo de recursos ou na assinatura. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Lista os contêineres de armazenamento associados com uma conta de armazenamento. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Remove o contêiner de armazenamento específico. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-powershell.md).
