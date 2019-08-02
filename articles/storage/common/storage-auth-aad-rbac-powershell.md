---
title: Usar o Azure PowerShell para gerenciar os direitos de acesso do AD do Azure para dados de blob e fila com RBAC – armazenamento do Azure
description: Use o Azure PowerShell para atribuir acesso a contêineres e filas com controle de acesso baseado em função (RBAC). O armazenamento do Azure dá suporte a funções RBAC internas e personalizadas para a autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bf888b72cca806822ca7a37542e71a5be0c8d5c3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443733"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Conceder acesso a dados de blob e fila do Azure com o RBAC, usando o PowerShell

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, grupo, uma entidade de serviço de aplicativo, ou um [identidade de recursos do Azure gerenciado](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o Azure PowerShell para listar funções RBAC internas e atribuí-los aos usuários. Para obter mais informações sobre como usar o Azure PowerShell, consulte [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Funções RBAC disponíveis da lista

Para listar as funções RBAC internas disponíveis com o Azure PowerShell, use o [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) comando:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Você verá as funções internas de dados de armazenamento do Azure listadas, junto com outras funções internas para o Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Atribuir uma função RBAC para uma entidade de segurança

Para atribuir uma função RBAC a uma entidade de segurança, use o [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) comando. O formato do comando pode mudar com base no escopo da atribuição. Os exemplos a seguir mostram como atribuir uma função a um usuário em vários escopos, mas você pode usar o mesmo comando para atribuir uma função a qualquer entidade de segurança.

### <a name="container-scope"></a>Escopo do contêiner

Para atribuir uma função no escopo para um contêiner, especifique uma cadeia de caracteres que contém o escopo do contêiner para o `--scope` parâmetro. O escopo para um contêiner está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo a seguir atribui a **Colaborador de dados de Blob de armazenamento** função para um usuário no escopo em um contêiner denominado *contêiner de exemplo*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Escopo da fila

Para atribuir uma função no escopo para uma fila, especifique uma cadeia de caracteres que contém o escopo da fila para o `--scope` parâmetro. O escopo para uma fila está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo a seguir atribui a **Colaborador de dados da fila de armazenamento** função para um usuário no escopo para uma fila denominada *fila de exemplo*. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Escopo da conta de armazenamento

Para atribuir uma função no escopo para a conta de armazenamento, especifique o escopo do recurso de conta de armazenamento para o `--scope` parâmetro. O escopo para uma conta de armazenamento está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A exemplo a seguir mostra como escopo o **leitor de dados de Blob de armazenamento** função a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Escopo do grupo de recursos

Para atribuir uma função no escopo para o grupo de recursos, especifique o nome do grupo de recursos ou a ID para o `--resource-group` parâmetro. O exemplo a seguir atribui a **leitor de dados do armazenamento de fila** função a um usuário no nível do grupo de recursos. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado nos colchetes pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Escopo da assinatura

Para atribuir uma função no escopo da assinatura, especifique o escopo para a assinatura para o `--scope` parâmetro. O escopo para uma assinatura está no formato:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a **leitor de dados de Blob de armazenamento** função a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
