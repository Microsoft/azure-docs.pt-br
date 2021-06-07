---
title: Exemplo de Script da CLI do Azure – Adicionar um aplicativo no Lote
description: Este script de exemplo demonstra como adicionar um aplicativo para uso com um pool ou uma tarefa do Lote do Azure.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765255"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Exemplo de CLI: adicionar um aplicativo a uma conta de Lote do Azure

Este script demonstra como adicionar um aplicativo para uso com um pool ou tarefa do Lote do Azure. Para configurar um aplicativo para adicionar sua conta do Lote, coloque seu executável, junto com quaisquer dependências, em um arquivo .zip. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

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
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Lote. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Criar um aplicativo.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Adiciona um pacote de aplicativos ao aplicativo especificado.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Atualiza as propriedades de um aplicativo.  |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
