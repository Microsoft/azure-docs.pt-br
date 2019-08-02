---
title: Exemplo de Script da CLI do Azure – Adicionar um aplicativo no Lote | Microsoft Docs
description: Exemplo de Script da CLI do Azure – Adicionar um aplicativo no Lote
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: ba7f64910e033751df7d158a6fddf36371f2933f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322253"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Exemplo de CLI: Adicionar um aplicativo a uma conta de Lote do Azure

Este script demonstra como adicionar um aplicativo para uso com um pool ou tarefa do Lote do Azure. Para configurar um aplicativo para adicionar sua conta do Lote, coloque seu executável, junto com quaisquer dependências, em um arquivo .zip. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir.
Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Lote. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Criar um aplicativo.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Adiciona um pacote de aplicativos ao aplicativo especificado.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Atualiza as propriedades de um aplicativo.  |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
