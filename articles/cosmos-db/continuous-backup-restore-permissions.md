---
title: Configure permissões para restaurar uma conta de Azure Cosmos DB.
description: Saiba como isolar e restringir as permissões de restauração para a conta de backup contínuo a uma função específica ou a uma entidade de segurança. Ele mostra como atribuir uma função interna usando portal do Azure, CLI ou definir uma função personalizada.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 82af70547d20509c48f1e07bbc7610fc666a6da1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393047"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Gerenciar permissões para restaurar uma conta de Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB permite isolar e restringir as permissões de restauração para a conta de backup contínuo (versão prévia) a uma função específica ou a uma entidade de segurança. O proprietário da conta pode disparar uma restauração e atribuir uma função a outras entidades de segurança para executar a operação de restauração. Essas permissões podem ser aplicadas no escopo da assinatura ou de forma mais granular no escopo da conta de origem, conforme mostrado na imagem a seguir:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista de funções necessárias para executar a operação de restauração." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Escopo é um conjunto de recursos que têm acesso para saber mais sobre escopos, consulte a documentação do [RBAC do Azure](../role-based-access-control/scope-overview.md) . No Azure Cosmos DB, os escopos aplicáveis são a assinatura de origem e a conta de banco de dados para a maioria dos casos de uso. A entidade de segurança que executa as ações de restauração deve ter permissões de gravação para o grupo de recursos de destino.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Atribuir funções para restauração usando o portal do Azure

Para executar uma restauração, um usuário ou uma entidade de segurança precisa da permissão para restaurar (que é a permissão de *restauração/ação* ) e a permissão para provisionar uma nova conta (que é a permissão de *gravação* ).  Para conceder essas permissões, o proprietário pode atribuir as `CosmosRestoreOperator` `Cosmos DB Operator` funções e internas a uma entidade de segurança.

1. Entre no [Portal do Azure](https://portal.azure.com/)

1. Navegue até a sua assinatura e vá para a guia **controle de acesso (iam)** e selecione **Adicionar**  >  **Adicionar atribuição de função**

1. No painel **Adicionar atribuição de função** , para campo de **função** , selecione função **CosmosRestoreOperator** . Escolha **usuário, grupo ou uma entidade de serviço** para o campo **atribuir acesso a** e pesquise por um nome de usuário ou ID de email, conforme mostrado na imagem a seguir:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Atribua funções de operador CosmosRestoreOperator e Cosmos DB." border="true":::

1. Selecione **salvar** para conceder a permissão de *restauração/ação* .

1. Repita a etapa 3 com Cosmos DB função de **operador** para conceder a permissão de gravação. Ao atribuir essa função da portal do Azure, ela concede a permissão de restauração para toda a assinatura.

## <a name="permission-scopes"></a>Escopos de permissão

|Escopo  |Exemplo  |
|---------|---------|
|Assinatura | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Grupo de recursos | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Recurso de conta restaurável CosmosDB | /subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/Locations/oeste dos EUA/restorableDatabaseAccounts/23e99a35-CD36-4df4-9614-f767a03b9995|

O recurso de conta restaurável pode ser extraído da saída do `az cosmosdb restorable-database-account list --name <accountname>` comando na CLI ou `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` no cmdlet no PowerShell. O atributo Name na saída representa o `instanceID` da conta restaurável. Para saber mais, confira o artigo sobre o [PowerShell](continuous-backup-restore-powershell.md) ou a [CLI](continuous-backup-restore-command-line.md) .

## <a name="permissions"></a>Permissões

As permissões a seguir são necessárias para executar as diferentes atividades referentes à restauração para contas de modo de backup contínuo:

|Permissão  |Impacto  |Escopo mínimo  |Escopo máximo  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Essas permissões são necessárias para a implantação do modelo ARM para criar a conta restaurada. Consulte a permissão de exemplo [RestorableAction](#custom-restorable-action) abaixo para saber como definir essa função. | Não aplicável | Não aplicável  |
|Microsoft.DocumentDB/databaseAccounts/write | Essa permissão é necessária para restaurar uma conta em um grupo de recursos | Grupo de recursos sob o qual a conta restaurada é criada. | Assinatura sob a qual a conta restaurada é criada |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Essa permissão é necessária no escopo da conta de banco de dados restaurável de origem para permitir que as ações de restauração sejam executadas nela.  | O recurso *RestorableDatabaseAccount* que pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela `ID` Propriedade do recurso de conta de banco de dados restaurável. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>* | A assinatura que contém a conta de banco de dados restaurável. O grupo de recursos não pode ser escolhido como escopo.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Essa permissão é necessária no escopo da conta de banco de dados restaurável de origem para listar as contas de banco de dados que podem ser restauradas.  | O recurso *RestorableDatabaseAccount* que pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela `ID` Propriedade do recurso de conta de banco de dados restaurável. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| A assinatura que contém a conta de banco de dados restaurável. O grupo de recursos não pode ser escolhido como escopo.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Essa permissão é necessária no escopo da conta restaurável de origem para permitir a leitura de recursos restauráveis, como a lista de bancos de dados e contêineres para uma conta restaurável.  | O recurso *RestorableDatabaseAccount* que pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela `ID` Propriedade do recurso de conta de banco de dados restaurável. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| A assinatura que contém a conta de banco de dados restaurável. O grupo de recursos não pode ser escolhido como escopo. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>CLI do Azure cenários de atribuição de função para restaurar em escopos diferentes

Funções com permissão podem ser atribuídas a escopos diferentes para obter um controle granular sobre quem pode executar a operação de restauração em uma assinatura ou em uma determinada conta.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Atribuir capacidade de restauração de qualquer conta restaurável em uma assinatura

Atribuir a `CosmosRestoreOperator` função interna no nível de assinatura

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Atribuir capacidade de restauração de uma conta específica

* Atribua uma ação de gravação do usuário no grupo de recursos específico. Essa ação é necessária para criar uma nova conta no grupo de recursos.

* Atribua a função interna *CosmosRestoreOperator* à conta de banco de dados restaurável específica que precisa ser restaurada. No comando a seguir, o escopo para o *RestorableDatabaseAccount* é recuperado da `ID` Propriedade na saída de `az cosmosdb restorable-database-account` (se estiver usando a CLI) ou  `Get-AzCosmosDBRestorableDatabaseAccount` (se estiver usando o PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Atribuir capacidade de restauração de qualquer conta de origem em um grupo de recursos.
Não há suporte para esta operação no momento.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Criação de função personalizada para ação de restauração com a CLI

O proprietário da assinatura pode fornecer a permissão para restaurar para qualquer outra identidade do Azure AD. A permissão Restore baseia-se na ação: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` , e deve ser incluída em sua permissão Restore. Há uma função interna chamada *CosmosRestoreOperator* que tem essa função incluída. Você pode atribuir a permissão usando essa função interna ou criar uma função personalizada.

O RestorableAction abaixo representa uma função personalizada. Você precisa criar essa função explicitamente. O modelo JSON a seguir cria uma função personalizada *RestorableAction* com a permissão Restore:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Em seguida, use o seguinte comando de implantação de modelo para criar uma função com a permissão de restauração usando o modelo ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando o [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
