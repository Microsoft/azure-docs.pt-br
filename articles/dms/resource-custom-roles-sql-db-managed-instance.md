---
title: 'Funções personalizadas: migrações online do SQL Server para a Instância Gerenciada de SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a usar as funções personalizadas para as migrações online do SQL Server para a Instância Gerenciada de SQL do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1228234b6a2904c453ec92f3c09a7b3f55604953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363756"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Funções personalizadas para as migrações online do SQL Server para a Instância Gerenciada de SQL do Azure

O Serviço de Migração de Banco de Dados do Azure usa uma ID de aplicativo para interagir com os serviços do Azure. A ID do Aplicativo exige a função Colaborador no nível da assinatura (o que muitos departamentos de segurança corporativa não permitirão) ou a criação de funções personalizadas que concedem as permissões específicas necessárias para o Serviço de Migração de Banco de Dados do Azure. Como há um limite de duas mil funções personalizadas no Azure Active Directory, o ideal é combinar todas as permissões necessárias especificamente pela ID do Aplicativo em uma ou duas funções personalizadas e conceder à ID do Aplicativo a função personalizada em objetos ou grupos de recursos específicos (em vez de no nível da assinatura). Se o número de funções personalizadas não for uma preocupação, você poderá dividir as funções personalizadas por tipo de recurso para criar três funções personalizadas no total, conforme descrito abaixo.

A seção AssignableScopes da cadeia de caracteres JSON de definição de função permite que você controle o local em que as permissões serão exibidas na interface do usuário de **Adicionar Atribuição de Função** no portal. Provavelmente, é útil definir a função no grupo de recursos ou, até mesmo, no nível do recurso para evitar acumular funções extras na interface do usuário. Observe que isso não executa a atribuição de função real.

## <a name="minimum-number-of-roles"></a>Número mínimo de funções

No momento, recomendamos criar, no mínimo, duas funções personalizadas para a ID do Aplicativo: uma no nível do recurso e outra no nível da assinatura.

> [!NOTE]
> O último requisito de função personalizada pode acabar sendo removido, conforme o novo código da Instância Gerenciada de SQL é implantado no Azure.

**Função personalizada para a ID do Aplicativo**. Essa função é necessária para a migração do Serviço de Migração de Banco de Dados do Azure no nível do *recurso* ou do *grupo de recursos* que hospeda o Serviço de Migração de Banco de Dados do Azure (para obter mais informações sobre a ID do Aplicativo, confira o artigo [Usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Função personalizada para a ID do Aplicativo – assinatura**. Essa função é necessária para a migração do Serviço de Migração de Banco de Dados do Azure no nível da *assinatura* que hospeda a Instância Gerenciada de SQL.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

O JSON acima precisa ser armazenado em três arquivos de texto, e você pode usar os cmdlets do AzureRM, do AZ PowerShell ou da CLI do Azure para criar as funções usando **New-AzureRmRoleDefinition (AzureRM)** ou **New-AzRoleDefinition (AZ)** .

Para obter mais informações, confira o artigo [Funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

Depois de criar essas funções personalizadas, você precisará adicionar atribuições de função aos usuários e às IDs de Aplicativo aos recursos ou aos grupos de recursos apropriados:

* A “Função do DMS – ID do Aplicativo” precisa ser concedida à ID do Aplicativo que será usada para as migrações e nos níveis dos recursos da conta de armazenamento, da instância do Serviço de Migração de Banco de Dados do Azure e da Instância Gerenciada de SQL. Ela é concedida no nível do recurso ou do grupo de recursos que hospeda o Serviço de Migração de Banco de Dados do Azure.
* A “Função do DMS – ID do Aplicativo – Assinatura” precisa ser concedida à ID do Aplicativo no nível da assinatura que hospeda a Instância Gerenciada de SQL (a concessão no recurso ou no grupo de recursos falhará). Esse requisito será temporário até que uma atualização de código seja implantada.

## <a name="expanded-number-of-roles"></a>Número expandido de funções

Se o número de funções personalizadas no Azure Active Directory não for uma preocupação, recomendaremos que você crie um total de três funções. Você ainda precisará da “Função do DMS – ID do Aplicativo – Assinatura”, mas a “Função do DMS – ID do Aplicativo” acima será dividida por tipo de recurso em duas funções diferentes.

**Função personalizada para a ID do Aplicativo para a Instância Gerenciada de SQL**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Função personalizada para a ID do Aplicativo para o Armazenamento**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Atribuição de função

Para atribuir uma função aos usuários/à ID do Aplicativo, abra o portal do Azure e execute as seguintes etapas:

1. Procure o grupo de recursos ou o recurso (com exceção da função que precisa ser concedida na assinatura), acesse **Controle de Acesso** e role a página para localizar as funções personalizadas recém-criadas.

2. Selecione a função apropriada e a ID do Aplicativo e salve as alterações.

  Suas IDs de Aplicativo agora serão listadas na guia **Atribuições de função**.

## <a name="next-steps"></a>Próximas etapas

* Examine as diretrizes de migração para o seu cenário no [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/) da Microsoft.
