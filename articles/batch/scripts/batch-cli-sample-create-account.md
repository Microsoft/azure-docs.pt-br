---
title: Exemplo de Script da CLI do Azure – Criar Conta do Lote – Serviço em lotes | Microsoft Docs
description: Exemplo de Script da CLI do Azure – Criar uma conta do Lote em modo de Serviço em lotes
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
ms.openlocfilehash: 038cea1aa8d72d46d028873fb4be19887b83d787
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322222"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Exemplo de CLI: Criar uma conta do Lote no modo de serviço do Lote

Esse script cria uma conta do Lote do Azure em modo de Serviço em lotes e mostra como consultar ou atualizar várias propriedades da conta. Quando você cria uma conta do Lote no modo de Serviço em lotes padrão, os nós de computação são atribuídos internamente pelo Serviço em lotes. Nós de computação alocados estão sujeitos a uma cota vCPU (núcleo) separada, e a conta pode ser autenticada por meio de credenciais de chave compartilhada ou de um token do Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Lote. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Atualiza as propriedades da conta do Lote.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Recupera os detalhes da conta do Lote especificada.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Recupera as chaves de acesso da conta do Lote especificada.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
