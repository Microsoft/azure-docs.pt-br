---
title: Atribuir uma política de acesso de Azure Key Vault (CLI)
description: Como usar o CLI do Azure para atribuir uma política de acesso de Key Vault a uma entidade de serviço ou a uma identidade de aplicativo.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 0c7910ac149c8de43eeac92913a0d314fcc1854e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934570"
---
# <a name="assign-a-key-vault-access-policy"></a>Atribuir uma política de acesso de Key Vault

Uma política de acesso Key Vault determina se uma determinada entidade de serviço, ou seja, um aplicativo ou grupo de usuários, pode executar operações diferentes em Key Vault [segredos](../secrets/index.yml), [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Você pode atribuir políticas de acesso usando o [portal do Azure](assign-access-policy-portal.md), o CLI do Azure (este artigo) ou [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre como criar grupos em Azure Active Directory usando o CLI do Azure, consulte [AZ ad Group Create](/cli/azure/ad/group#az-ad-group-create) e [AZ ad Group Member Add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configurar o CLI do Azure e entrar

1. Para executar comandos do CLI do Azure localmente, instale o [CLI do Azure](/cli/azure/install-azure-cli).
 
    Para executar comandos diretamente na nuvem, use o [Azure cloud Shell](../../cloud-shell/overview.md).

1. Somente CLI local: entrar no Azure usando `az login` :

    ```bash
    az login
    ```

    O `az login` comando abre uma janela do navegador para coletar credenciais, se necessário.

## <a name="acquire-the-object-id"></a>Adquirir a ID de objeto

Determine a ID de objeto do aplicativo, do grupo ou do usuário ao qual você deseja atribuir a política de acesso:

- Aplicativos e outras entidades de serviço: Use o comando [AZ ad SP List](/cli/azure/ad/sp#az-ad-sp-list) para recuperar suas entidades de serviço. Examine a saída do comando para determinar a ID de objeto da entidade de segurança à qual você deseja atribuir a política de acesso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupos: Use o comando [AZ ad Group List](/cli/azure/ad/group#az-ad-group-list) , filtrando os resultados com o `--display-name` parâmetro:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Usuários: Use o comando [AZ ad User show](/cli/azure/ad/user#az-ad-user-show) , passando o endereço de email do usuário no `--id` parâmetro:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Atribuir a política de acesso
    
Use o comando [AZ keyvault Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) para atribuir as permissões desejadas:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Substitua `<object-id>` pela ID de objeto da entidade de serviço.

Você só precisa incluir `--secret-permissions` , `--key-permissions` e `--certificate-permissions` ao atribuir permissões a esses tipos específicos. Os valores permitidos para `<secret-permissions>` , `<key-permissions>` e `<certificate-permissions>` são fornecidos na documentação do [conjunto de chaves AZ keyvault – Policy](/cli/azure/keyvault#az-keyvault-set-policy) .

## <a name="next-steps"></a>Próximas etapas

- [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](security-overview.md#identity-management)
- [Proteja seu cofre de chaves](secure-your-key-vault.md).
- [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)