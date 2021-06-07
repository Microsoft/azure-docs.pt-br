---
title: Configure as permissões para restaurar uma conta do Azure Cosmos DB.
description: Saiba como isolar e restringir as permissões de restauração da conta de backup contínuo a uma função específica ou a uma entidade de segurança. Isso mostra como atribuir uma função interna usando o portal do Azure, a CLI ou definindo uma função personalizada.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100648596"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Gerenciar as permissões para restaurar uma conta do Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para o Azure Cosmos DB está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Cosmos DB permite isolar e restringir as permissões de restauração da conta de backup contínuo (versão prévia) a uma função específica ou a uma entidade de segurança. O proprietário da conta pode disparar uma restauração e atribuir uma função a outras entidades de segurança para executar a operação de restauração. Essas permissões podem ser aplicadas no escopo da assinatura ou, de maneira mais granular, no escopo da conta de origem, conforme mostrado na seguinte imagem:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista de funções exigidas para executar a operação de restauração." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Escopo é um conjunto de recursos que têm acesso. Para saber mais sobre escopos, confira a documentação do [RBAC do Azure](../role-based-access-control/scope-overview.md). No Azure Cosmos DB, os escopos aplicáveis são a assinatura de origem e a conta de banco de dados para a maioria dos casos de uso. A entidade de segurança que executa as ações de restauração deve ter permissões de gravação sobre o grupo de recursos de destino.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Atribuir funções para restaurar usando o portal do Azure

Para executar uma restauração, o usuário ou a entidade de segurança precisa de permissão para restaurar (ou seja, permissão de *restauração/ação*) e para provisionar uma nova conta (ou seja, permissão de *gravação*).  Para conceder essas permissões, o proprietário pode atribuir as funções internas `CosmosRestoreOperator` e `Cosmos DB Operator` a uma entidade de segurança.

1. Entre no [Portal do Azure](https://portal.azure.com/)

1. Navegue até a sua assinatura e vá para a guia **Controle de Acesso (IAM)** e selecione **Adicionar** > **Adicionar atribuição de função**

1. No painel **Adicionar atribuição de função**, para o campo **Função**, selecione a função **CosmosRestoreOperator**. Escolha **Usuário, grupo ou uma entidade de serviço** para o campo **Atribuir acesso a** e pesquise por um nome de usuário ou uma ID de email, conforme mostrado na seguinte imagem:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Atribua as funções de CosmosRestoreOperator e Operador de Cosmos DB." border="true":::

1. Selecione **Salvar** para conceder a permissão de *restauração/ação*.

1. Repita a Etapa 3 com a função **Operador do Cosmos DB** para conceder a permissão de gravação. Ao atribuir essa função do portal do Azure, ela concede a permissão de restauração para toda a assinatura.

## <a name="permission-scopes"></a>Escopos de permissão

|Escopo  |Exemplo  |
|---------|---------|
|Subscription | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Recurso de conta restaurável do CosmosDB | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

O recurso de conta restaurável pode ser extraído da saída do comando `az cosmosdb restorable-database-account list --name <accountname>` na CLI ou do cmdlet `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` no PowerShell. O atributo Nome, na saída, representa o `instanceID` da conta restaurável. Para saber mais, confira o artigo sobre o [PowerShell](continuous-backup-restore-powershell.md) ou a [CLI](continuous-backup-restore-command-line.md).

## <a name="permissions"></a>Permissões

As seguintes permissões são necessárias para executar as diferentes atividades referentes à restauração para contas no modo de backup contínuo:

|Permissão  |Impacto  |Escopo mínimo  |Escopo máximo  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Essas permissões são necessárias para a implantação do modelo do ARM a fim de criar a conta restaurada. Confira a permissão de exemplo [RestorableAction](#custom-restorable-action) abaixo para saber como definir essa função. | Não aplicável | Não aplicável  |
|`Microsoft.DocumentDB/databaseAccounts/write` | Essa permissão é necessária para restaurar uma conta em um grupo de recursos | Grupo de recursos sob o qual a conta restaurada é criada. | Assinatura sob a qual a conta restaurada é criada |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Essa permissão é necessária no escopo da conta restaurável do banco de dados de origem a fim de permitir que as ações de restauração sejam executadas nela.  | O recurso *RestorableDatabaseAccount* pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela propriedade `ID` do recurso da conta restaurável do banco de dados. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>* | A assinatura que contém a conta restaurável do banco de dados. O grupo de recursos não pode ser escolhido como escopo.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Essa permissão é necessária no escopo da conta restaurável do banco de dados de origem para listar as contas do banco de dados que podem ser restauradas.  | O recurso *RestorableDatabaseAccount* pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela propriedade `ID` do recurso da conta restaurável do banco de dados. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>*| A assinatura que contém a conta restaurável do banco de dados. O grupo de recursos não pode ser escolhido como escopo.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Essa permissão é necessária no escopo da conta restaurável de origem para permitir a leitura de recursos restauráveis, como a lista de bancos de dados e contêineres para uma conta restaurável.  | O recurso *RestorableDatabaseAccount* pertence à conta de origem que está sendo restaurada. Esse valor também é fornecido pela propriedade `ID` do recurso da conta restaurável do banco de dados. Um exemplo de conta restaurável é */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>*| A assinatura que contém a conta restaurável do banco de dados. O grupo de recursos não pode ser escolhido como escopo. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Cenários de atribuição de função da CLI do Azure para restaurar em diferentes escopos

Funções com permissão podem ser atribuídas a diferentes escopos para obter um controle granular sobre quem pode executar a operação de restauração em uma assinatura ou em determinada conta.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Atribuir a capacidade de restauração com base em qualquer conta restaurável em uma assinatura

Atribuir a função interna `CosmosRestoreOperator` no nível da assinatura

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Atribuir a capacidade de restaurar com base em uma conta específica

* Atribua uma ação de gravação do usuário no grupo de recursos específico. Essa ação é obrigatória para criar uma conta no grupo de recursos.

* Atribua a função interna *CosmosRestoreOperator* à conta de banco de dados restaurável específica que precisa ser restaurada. No comando a seguir, o escopo de *RestorableDatabaseAccount* é recuperado da propriedade `ID` na saída de `az cosmosdb restorable-database-account` (se estiver usando a CLI) ou `Get-AzCosmosDBRestorableDatabaseAccount` (se estiver usando o PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Atribua a capacidade de restaurar com base em qualquer conta de origem em um grupo de recursos.
Essa operação não tem suporte no momento.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Criação de função personalizada para ação de restauração com a CLI

O proprietário da assinatura pode fornecer a permissão para restaurar em qualquer outra identidade do Azure AD. A permissão de restauração baseia-se na ação `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` e deve ser incluída na permissão de restauração dela. Há uma função interna chamada *CosmosRestoreOperator* que tem essa função incluída. Você pode atribuir a permissão usando essa função interna ou criar uma função personalizada.

A RestorableAction, abaixo, representa uma função personalizada. Você precisa criar essa função explicitamente. O seguinte modelo JSON cria uma função personalizada *RestorableAction* com permissão de restauração:

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

Depois, use o seguinte comando de implantação de modelo para criar uma função com a permissão de restauração usando o modelo do ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando o [portal do Azure](continuous-backup-restore-portal.md), o [PowerShell](continuous-backup-restore-powershell.md), a[CLI](continuous-backup-restore-command-line.md) ou o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
