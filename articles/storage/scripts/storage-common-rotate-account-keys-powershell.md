---
title: Girar chaves de acesso da conta de armazenamento com o PowerShell
titleSuffix: Azure Storage
description: Crie uma conta de Armazenamento do Azure e, em seguida, recupere e recicle uma de suas chaves de acesso da conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070421"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Girar chaves de acesso da conta de armazenamento com o PowerShell

Esse script cria uma conta de Armazenamento do Azure, exibe a chave de acesso primária da nova conta de armazenamento e, em seguida, renova (recicla) a chave.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar a conta de armazenamento e recuperar e reciclar uma de suas chaves de acesso. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Obtém todas as localizações e os provedores de recursos com suporte para cada localização. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma conta de armazenamento. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtém as chaves de acesso para a conta de Armazenamento do Azure. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Regenera uma chave de acesso para a conta de Armazenamento do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-powershell.md).
