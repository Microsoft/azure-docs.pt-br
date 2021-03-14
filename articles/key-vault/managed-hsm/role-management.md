---
title: Gerenciamento da função de plano de dados do HSM Gerenciado – Azure Key Vault | Microsoft Docs
description: Use este artigo para gerenciar as atribuições de função do HSM Gerenciado
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a4cc898744109475bc119f37350d1b689c550f58
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209553"
---
# <a name="managed-hsm-role-management"></a>Gerenciamento de função do HSM Gerenciado

> [!NOTE]
> O Key Vault é compatível com dois tipos de recurso: cofres e HSMs Gerenciados. Este artigo é sobre o **HSM Gerenciado**. Se você quiser aprender como gerenciar um cofre, confira [Gerenciar o Key Vault usando a CLI do Azure](../general/manage-with-cli2.md).

Para obter uma visão geral do HSM Gerenciado, confira [O que é o HSM Gerenciado?](overview.md). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este artigo mostra como gerenciar funções para um plano de dados do HSM Gerenciado. Para aprender sobre o modelo de controle de acesso do HSM Gerenciado, confira [Controle de acesso do HSM Gerenciado](access-control.md).

Para permitir que uma entidade de segurança (como um usuário, uma entidade de serviço, um grupo ou uma identidade gerenciada) execute operações do plano de dados do HSM Gerenciado, é necessário atribuir a ela uma função que permita a execução dessas operações. Por exemplo, se você quiser permitir que um aplicativo execute uma operação de assinatura usando uma chave, será necessário atribuir a ele uma função que contenha "Microsoft.KeyVault/managedHSM/keys/sign/action" como uma das ações de dados. Uma função pode ser atribuída em um escopo específico. O RBAC local do HSM Gerenciado é compatível com dois escopos: em todo o HSM (`/` ou `/keys`) e por chave (`/keys/<keyname>`).

Para obter uma lista com todas as funções internas do HSM Gerenciado e as operações que elas permitem, confira [Funções internas do HSM Gerenciado](built-in-roles.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar comandos da CLI do Azure neste artigo, você deve ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM Gerenciado na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM Gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM Gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para obter mais informações sobre as opções de logon por meio da CLI, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli)

## <a name="create-a-new-role-assignment"></a>Criar uma atribuição de função

### <a name="assign-roles-for-all-keys"></a>Atribuir funções para todas as chaves

Use o comando `az keyvault role assignment create` para atribuir a função de **Responsável pela Criptografia do HSM Gerenciado** ao usuário identificado pelo nome UPN **user2\@contoso.com** para todas as **chaves** (escopo `/keys`) do ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Atribuir função para uma chave específica

Use o comando `az keyvault role assignment create` para atribuir a função de **Responsável pela Criptografia do HSM Gerenciado** ao usuário identificado pelo nome UPN **user2\@contoso.com** para uma chave específica chamada **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Listar as atribuições de função existentes

Use `az keyvault role assignment list` para listar as atribuições de função.

Todas as atribuições de função no escopo/(padrão, quando nenhum --scope for especificado) para todos os usuários (padrão, quando nenhum --assignee for especificado)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Todas as atribuições de função no nível do HSM para um usuário específico **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Quando o escopo for / (ou /chaves), o comando listará apenas todas as atribuições de função no nível superior e não mostrará as atribuições de função no nível de chave individual.

Todas as atribuições de função para um usuário específico **user2@contoso.com** para uma chave específica chamada **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Uma atribuição de função específica para a função de **Responsável pela Criptografia do HSM Gerenciado** para um usuário específico **user2@contoso.com** para uma chave específica chamada **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Excluir uma atribuição de função

Use o comando `az keyvault role assignment delete` para excluir a função de **Responsável pela Criptografia do HSM Gerenciado** atribuída ao usuário **user2\@contoso.com** para a chave **user2contoso**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Liste todas as definições de função disponíveis

Use o comando `az keyvault role definition list` para listar todas as definições de função.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Próximas etapas

- Obtenha uma visão geral do [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md).
- Confira um tutorial sobre [Gerenciamento de funções no HSM Gerenciado](role-management.md)
- Saiba mais sobre o [modelo de controle de acesso do HSM Gerenciado](access-control.md)
- Confira todas as [funções internas para RBAC local do HSM Gerenciado](built-in-roles.md)
