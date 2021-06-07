---
title: Usar o PowerShell para atribuir uma função do Azure para acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar o módulo Azure PowerShell para atribuir permissões a uma entidade de segurança de Azure Active Directory com o controle de acesso baseado em função do Azure (RBAC do Azure). O armazenamento do Azure dá suporte a funções personalizadas e internas do Azure para autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e4fea52f56da9f2e84746daf0121df0b4355411
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375945"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Usar o PowerShell para atribuir uma função do Azure para acesso a dados de BLOB e de fila

O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar contêineres ou filas.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar Azure PowerShell para listar funções internas do Azure e atribuí-las aos usuários. Para obter mais informações sobre como usar Azure PowerShell, consulte [visão geral do Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Funções do Azure para BLOBs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Listar funções do Azure disponíveis

Para listar funções internas do Azure disponíveis com Azure PowerShell, use o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Você verá as funções de dados de armazenamento do Azure internas listadas, junto com outras funções internas do Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Atribuir uma função do Azure a uma entidade de segurança

Para atribuir uma função do Azure a uma entidade de segurança, use o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . O formato do comando pode diferir com base no escopo da atribuição. Para executar o comando, você precisa ter a função de proprietário ou colaborador atribuída no escopo correspondente. Os exemplos a seguir mostram como atribuir uma função a um usuário em vários escopos, mas você pode usar o mesmo comando para atribuir uma função a qualquer entidade de segurança.

> [!IMPORTANT]
> Ao criar uma conta de armazenamento do Azure, você não recebe automaticamente permissões para acessar dados por meio do Azure AD. Você deve atribuir explicitamente a si mesmo uma função de RBAC do Azure para acesso a dados. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila.
>
> Se a conta de armazenamento estiver bloqueada com um bloqueio de Azure Resource Manager somente leitura, o bloqueio impedirá a atribuição de funções RBAC do Azure que estão no escopo da conta de armazenamento ou em um contêiner de dados (contêiner ou fila de BLOB).

### <a name="container-scope"></a>Escopo do contêiner

Para atribuir uma função com escopo a um contêiner, especifique uma cadeia de caracteres que contenha o escopo do contêiner para o `--scope` parâmetro. O escopo de um contêiner está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** a um usuário, cujo escopo é um contêiner chamado *de contêiner de exemplo*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Escopo da fila

Para atribuir uma função com escopo a uma fila, especifique uma cadeia de caracteres que contenha o escopo da fila para o `--scope` parâmetro. O escopo de uma fila está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo a seguir atribui a função de **colaborador de dados da fila de armazenamento** a um usuário, com escopo para uma fila chamada *Sample-Queue*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Escopo da conta de armazenamento

Para atribuir uma função com escopo à conta de armazenamento, especifique o escopo do recurso de conta de armazenamento para o `--scope` parâmetro. O escopo de uma conta de armazenamento está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo a seguir mostra como fazer o escopo da função de **leitor de dados de blob de armazenamento** para um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Escopo do grupo de recursos

Para atribuir uma função com escopo ao grupo de recursos, especifique o nome ou a ID do grupo de recursos para o `--resource-group` parâmetro. O exemplo a seguir atribui a função de **leitor de dados de fila de armazenamento** a um usuário no nível do grupo de recursos. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes por seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Escopo da assinatura

Para atribuir uma função com escopo à assinatura, especifique o escopo da assinatura para o `--scope` parâmetro. O escopo de uma assinatura está no formato:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a função de **leitor de dados de blob de armazenamento** a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função do Azure usando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Use o CLI do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](storage-auth-aad-rbac-cli.md)
- [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](storage-auth-aad-rbac-portal.md)
