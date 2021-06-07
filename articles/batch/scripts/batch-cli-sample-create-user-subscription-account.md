---
title: Exemplo de Script da CLI do Azure – Criar conta do Lote – assinatura do usuário
description: O script cria uma conta de Lote do Azure no modo de assinatura do usuário. Essa conta aloca nós de computação em sua assinatura.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bd7b7ac3dbb52ebafa00499e64ec3cff0969a13
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768320"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemplo da CLI: criar uma conta de Lote no modo de assinatura do usuário

O script cria uma conta de Lote do Azure no modo de assinatura do usuário. Uma conta que aloca nós de computação em sua assinatura deve ser autenticada por meio de um token do Azure Active Directory. Os nós de computação alocados contam para a cota de vCPU (núcleo) de sua assinatura. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.  

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az role assignment create](/cli/azure/role) | Crie uma nova atribuição de função para um usuário, grupo ou entidade de serviço. |
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Cria um cofre de chave. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | Atualize a política de segurança do cofre de chaves especificado. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Lote.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
