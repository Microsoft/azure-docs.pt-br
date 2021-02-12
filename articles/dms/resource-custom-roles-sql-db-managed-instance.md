---
title: 'Funções personalizadas: SQL Server online para migrações do SQL Instância Gerenciada'
titleSuffix: Azure Database Migration Service
description: Aprenda a usar as funções personalizadas para SQL Server para migrações do SQL Instância Gerenciada online do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: dad02735228bb639981bf3f053a74f29d1944e5a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961474"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Funções personalizadas para SQL Server para migrações do SQL Instância Gerenciada online do Azure

O serviço de migração de banco de dados do Azure usa uma ID de aplicativo para interagir com os serviços do Azure. A ID do aplicativo requer a função de colaborador no nível da assinatura (que muitos departamentos de segurança corporativa não permitirão) ou a criação de funções personalizadas que concedem as permissões específicas que o serviço de migrações de banco de dados do Azure exige. Como há um limite de 2.000 funções personalizadas em Azure Active Directory, talvez você queira combinar todas as permissões necessárias especificamente pela ID do aplicativo em uma ou duas funções personalizadas e, em seguida, conceder à ID do aplicativo a função personalizada em objetos ou grupos de recursos específicos (vs. no nível da assinatura). Se o número de funções personalizadas não for uma preocupação, você poderá dividir as funções personalizadas por tipo de recurso, para criar três funções personalizadas no total, conforme descrito abaixo.

A seção AssignableScopes da cadeia de caracteres JSON de definição de função permite que você controle onde as permissões aparecem na interface do usuário de **atribuição de função** no Portal. Você provavelmente desejará definir a função no grupo de recursos ou até mesmo no nível de recurso para evitar a confusão da interface do usuário com funções extras. Observe que isso não executa a atribuição de função real.

## <a name="minimum-number-of-roles"></a>Número mínimo de funções

No momento, é recomendável criar no mínimo duas funções personalizadas para a ID do aplicativo, uma no nível de recurso e outra no nível de assinatura.

> [!NOTE]
> O último requisito de função personalizada pode eventualmente ser removido, pois o novo código do SQL Instância Gerenciada é implantado no Azure.

**Função personalizada para a ID do aplicativo**. Essa função é necessária para a migração do serviço de migração de banco de dados do Azure no nível de *recurso* ou *grupo de recursos* (para obter mais informações sobre a ID do aplicativo, consulte o artigo [usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)).

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

**Função personalizada para a ID do aplicativo-assinatura**. Essa função é necessária para a migração do serviço de migração de banco de dados do Azure no nível da *assinatura* .

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

O JSON acima deve ser armazenado em três arquivos de texto e você pode usar os cmdlets AzureRM, AZ PowerShell ou CLI do Azure para criar as funções usando **New-AzureRmRoleDefinition (AzureRM)** ou **New-AzRoleDefinition (AZ)**.

Para obter mais informações, consulte o artigo [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

Depois de criar essas funções personalizadas, você deve adicionar atribuições de função aos usuários e às IDs do aplicativo aos recursos ou grupos de recursos apropriados:

* A função "DMS função-ID do aplicativo" deve ser concedida à ID do aplicativo que será usada para as migrações e também na conta de armazenamento, instância do serviço de migração de banco de dados do Azure e níveis de recurso do SQL Instância Gerenciada.
* A função "função DMS-ID do aplicativo-sub" deve ser concedida à ID do aplicativo no nível da assinatura (a concessão do recurso ou do grupo de recursos falhará). Esse requisito é temporário até que uma atualização de código seja implantada.

## <a name="expanded-number-of-roles"></a>Número expandido de funções

Se o número de funções personalizadas na sua Azure Active Directory não for uma preocupação, recomendamos que você crie um total de três funções. Você ainda precisará da função "DMS de ID do aplicativo – sub", mas a função "DMS da função de diretório" acima é dividida por tipo de recurso em duas funções diferentes.

**Função personalizada para a ID do aplicativo para o SQL Instância Gerenciada**

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

**Função personalizada para a ID do aplicativo para armazenamento**

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

Para atribuir uma função à ID de usuários/aplicativos, abra o portal do Azure, execute as seguintes etapas:

1. Navegue até o grupo de recursos ou recurso (exceto para a função que precisa ser concedida na assinatura), acesse **controle de acesso** e role para localizar as funções personalizadas que você acabou de criar.

2. Selecione a função apropriada, selecione a ID do aplicativo e salve as alterações.

  Suas IDs de aplicativo agora aparecem listadas na guia **atribuições de função** .

## <a name="next-steps"></a>Próximas etapas

* Examine as diretrizes de migração para seu cenário no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.