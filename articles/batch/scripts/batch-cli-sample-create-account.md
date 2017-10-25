---
title: "Amostra de script da CLI do Azure – Criar uma conta do Lote | Microsoft Docs"
description: "Amostra de script da CLI do Azure – Criar uma conta do Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Criar uma conta do Lote com a CLI do Azure

Esse script cria uma conta do Lote do Azure e mostra como várias propriedades da conta podem ser consultadas e atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

Instale a CLI do Azure usando as instruções fornecidas no [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se ainda não tiver feito isso.

## <a name="batch-account-sample-script"></a>Script de exemplo de conta do Lote

Quando você cria uma conta do Lote, os nós de computação dessa conta são atribuídos internamente, por padrão, pelo serviço de Lote. Nós de computação alocados estarão sujeitos a uma cota de núcleo separada e a conta poderá ser autenticada por meio de credenciais de Chave Compartilhada ou um token do Azure Active Directory.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Conta do Lote usando o script de exemplo de assinatura de usuário

Você também pode optar por fazer com que o Lote crie seus nós de computação na sua própria assinatura do Azure.
Contas que alocam nós de computação em sua assinatura devem ser autenticadas por meio de um token do Azure Active Directory e os nós de computação distribuídos contarão para a sua cota de assinatura. Para criar uma conta nesse modo, uma pessoa deve especificar uma referência do Key Vault ao criar a conta.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>Limpar implantação

Depois de executar qualquer um dos scripts de exemplo acima, execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados (incluindo contas do Lote, contas de Armazenamento do Azure e cofres de chaves do Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos, uma conta do Lote e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Lote.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Atualiza as propriedades da conta do Lote.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Recupera os detalhes da conta do Lote especificada.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Recupera as chaves de acesso da conta do Lote especificada.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Cria um cofre de chave. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Atualize a política de segurança do cofre de chaves especificado. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação do Lote do Azure](../batch-cli-samples.md).
