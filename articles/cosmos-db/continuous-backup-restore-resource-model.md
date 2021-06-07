---
title: Modelo de recurso para a Azure Cosmos DB recurso de restauração pontual.
description: Este artigo explica o modelo de recurso para o recurso de restauração pontual do Azure Cosmos DB. Ele explica os parâmetros que permitem backup contínuo e aos recursos que podem ser restaurados na API do Azure Cosmos DB para contas SQL e MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 065127fbeaabc415dd9a5fbe74f90d5060909d5d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641031"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Modelo de recurso para a Azure Cosmos DB recurso de restauração pontual (versão prévia)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica o modelo de recurso para a Azure Cosmos DB recurso de restauração pontual (versão prévia). Ele explica os parâmetros que permitem backup contínuo e aos recursos que podem ser restaurados na API do Azure Cosmos DB para contas SQL e MongoDB.

## <a name="database-accounts-resource-model"></a>Modelo de recurso da conta do banco de dados

O modelo de recurso da conta do banco de dados é atualizado com algumas propriedades extras para dar suporte aos novos cenários de restauração. Essas propriedades são **BackupPolicy, e restoreparameters.**

### <a name="backuppolicy"></a>BackupPolicy

Uma nova propriedade na política de backup no nível da conta nomeada `Type` em `backuppolicy` parâmetro habilita o backup contínuo e as funcionalidades de restauração pontual. Esse modo é chamado de **backup contínuo**. Na visualização pública, você só pode definir esse modo ao criar a conta. Depois de habilitado, todos os contêineres e bancos de dados criados nessa conta terão o backup contínuo e as funcionalidades de restauração pontual habilitadas por padrão.

> [!NOTE]
> Atualmente, o recurso de restauração pontual está em visualização pública e está disponível para Azure Cosmos DB API para MongoDB e contas do SQL. Depois de criar uma conta com o modo contínuo, você não pode alterná-la para um modo periódico.

### <a name="createmode"></a>CreateMode

Essa propriedade indica como a conta foi criada. Os valores possíveis são *Default* e *Restore*. Para executar uma restauração, defina esse valor como *restaurar* e forneça os valores apropriados na `RestoreParameters` propriedade.

### <a name="restoreparameters"></a>Restoreparameters

O `RestoreParameters` recurso contém os detalhes da operação de restauração, incluindo a ID da conta, a hora de restauração e os recursos que precisam ser restaurados.

|Nome da propriedade |Descrição  |
|---------|---------|
|restaurarmode  | O modo de restauração deve ser *PointInTime* |
|repositório   |  A instanceId da conta de origem da qual a restauração será iniciada.       |
|restoreTimestampInUtc  | Ponto no tempo em UTC para o qual a conta deve ser restaurada. |
|databasesToRestore   | Lista de `DatabaseRestoreSource` objetos para especificar quais bancos de dados e contêineres devem ser restaurados. Se esse valor estiver vazio, a conta inteira será restaurada.   |

**DatabaseRestoreResource** -cada recurso representa um banco de dados individual e todas as coleções nesse banco de dados.

|Nome da propriedade |Descrição  |
|---------|---------|
|databaseName | O nome do banco de dados |
| coleções de| A lista de contêineres neste banco de dados |

### <a name="sample-resource"></a>Recurso de exemplo

O JSON a seguir é um recurso de conta de banco de dados de exemplo com backup contínuo habilitado:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Recursos restauráveis

Um conjunto de novos recursos e APIs está disponível para ajudá-lo a descobrir informações críticas sobre os recursos, que podem ser restaurados, locais onde eles podem ser restaurados e os carimbos de data/hora quando as principais operações foram executadas nesses recursos.

> [!NOTE]
> Toda a API usada para enumerar esses recursos requer as seguintes permissões:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Conta de banco de dados restaurável

Esse recurso contém uma instância de conta de banco de dados que pode ser restaurada. A conta do banco de dados pode ser uma conta excluída ou em tempo real. Ele contém informações que permitem que você localize a conta do banco de dados de origem que deseja restaurar.

|Nome da propriedade |Descrição  |
|---------|---------|
| ID | O identificador exclusivo do recurso. |
| accountName | O nome da conta de banco de dados global. |
| creationTime | A hora em UTC em que a conta foi criada.  |
| exclusãotime | A hora em UTC em que a conta foi excluída.  Esse valor estará vazio se a conta estiver ativa. |
| apiType | O tipo de API da conta de Azure Cosmos DB. |
| restorableLocations | A lista de locais onde a conta existia. |
| restorableLocations: LocationName | O nome da região da conta regional. |
| restorableLocations: regionalDatabaseAccountInstanceI | O GUID da conta regional. |
| restorableLocations: CreationTime | A hora em UTC em que a conta regional foi criada.|
| restorableLocations: excluitime | A hora em UTC em que a conta regional foi excluída. Esse valor estará vazio se a conta regional estiver ativa.|

Para obter uma lista de todas as contas restauráveis, consulte [contas de banco de dados restauráveis-lista](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/list) ou [contas de banco de dados restauráveis – listar por](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/listbylocation) artigos de localização.

### <a name="restorable-sql-database"></a>Banco de dados SQL restaurável

Cada recurso contém informações de um evento de mutação, como criação e exclusão que ocorreram no banco de dados SQL. Essas informações podem ajudar em cenários em que o banco de dados foi acidentalmente excluído e se você precisa descobrir quando esse evento ocorreu.

|Nome da propriedade |Descrição  |
|---------|---------|
| eventTimestamp | A hora em UTC quando o banco de dados é criado ou excluído. |
| ownerId | O nome do banco de dados SQL. |
| ownerResourceId | A ID de recurso do banco de dados SQL|
| operationType | O tipo de operação deste evento de banco de dados. Estes são os valores possíveis:<br/><ul><li>Criar: evento de criação de banco de dados</li><li>Excluir: evento de exclusão de banco de dados</li><li>Substituir: evento de modificação do banco de dados</li><li>SystemOperation: evento de modificação do banco de dados disparado pelo sistema. Este evento não é iniciado pelo usuário</li></ul> |
| Banco de Dados |As propriedades do banco de dados SQL no momento do evento|

Para obter uma lista de todas as mutações de banco de dados, consulte o artigo de [lista de bancos SQL restauráveis-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqldatabases/list) .

### <a name="restorable-sql-container"></a>Contêiner SQL restaurável

Cada recurso contém informações de um evento de mutação, como criação e exclusão que ocorreram no contêiner SQL. Essas informações podem ajudar em cenários em que o contêiner foi modificado ou excluído, e se você precisar descobrir quando o evento ocorreu.

|Nome da propriedade |Descrição  |
|---------|---------|
| eventTimestamp    | A hora em UTC em que esse evento de contêiner ocorreu.|
| ownerId| O nome do contêiner SQL.|
| ownerResourceId   | A ID de recurso do contêiner SQL.|
| operationType | O tipo de operação deste evento de contêiner. Estes são os valores possíveis: <br/><ul><li>Criar: evento de criação de contêiner</li><li>Excluir: evento de exclusão de contêiner</li><li>Replace: evento de modificação do contêiner</li><li>SystemOperation: evento de modificação de contêiner disparado pelo sistema. Este evento não é iniciado pelo usuário</li></ul> |
| contêiner | As propriedades do contêiner SQL no momento do evento.|

Para obter uma lista de todas as mutações de contêiner no mesmo banco de dados, consulte o artigo [contêineres SQL restauráveis-lista](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlcontainers/list) .

### <a name="restorable-sql-resources"></a>Recursos SQL restauráveis

Cada recurso representa um banco de dados individual e todos os contêineres nesse banco de dados.

|Nome da propriedade |Descrição  |
|---------|---------|
| databaseName  | O nome do banco de dados SQL.
| coleções de   | A lista de contêineres SQL neste banco de dados.|

Para obter uma lista de combinação de banco de dados SQL e contêiner existentes na conta no carimbo de data e hora e no local fornecidos, consulte o artigo [recursos SQL restauráveis – lista](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlresources/list) .

### <a name="restorable-mongodb-database"></a>Banco de dados MongoDB restaurável

Cada recurso contém informações de um evento de mutação, como criação e exclusão que ocorreram no banco de dados MongoDB. Essas informações podem ajudar no cenário em que o banco de dados foi acidentalmente excluído e o usuário precisa descobrir quando esse evento ocorreu.

|Nome da propriedade |Descrição  |
|---------|---------|
|eventTimestamp| A hora em UTC em que esse evento de banco de dados ocorreu.|
| ownerId| O nome do banco de dados MongoDB. |
| ownerResourceId   | A ID de recurso do banco de dados MongoDB. |
| operationType |   O tipo de operação deste evento de banco de dados. Estes são os valores possíveis:<br/><ul><li> Criar: evento de criação de banco de dados</li><li> Excluir: evento de exclusão de banco de dados</li><li> Substituir: evento de modificação do banco de dados</li><li> SystemOperation: evento de modificação do banco de dados disparado pelo sistema. Este evento não é iniciado pelo usuário </li></ul> |

Para obter uma lista de todas as mutações de banco de dados, consulte o artigo de [lista de bancos do MongoDB restauráveis-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbdatabases/list) .

### <a name="restorable-mongodb-collection"></a>Coleção restaurável do MongoDB

Cada recurso contém informações de um evento de mutação, como criação e exclusão que ocorreram na coleção do MongoDB. Essas informações podem ajudar em cenários em que a coleção foi modificada ou excluída, e o usuário precisa descobrir quando o evento ocorreu.

|Nome da propriedade |Descrição  |
|---------|---------|
| eventTimestamp |A hora em UTC em que esse evento de coleta ocorreu. |
| ownerId| O nome da coleção do MongoDB. |
| ownerResourceId   | A ID de recurso da coleção do MongoDB. |
| operationType |O tipo de operação deste evento de coleta. Estes são os valores possíveis:<br/><ul><li>Criar: evento de criação de coleção</li><li>Excluir: evento de exclusão de coleção</li><li>Substituir: evento de modificação da coleção</li><li>SystemOperation: evento de modificação de coleção disparado pelo sistema. Este evento não é iniciado pelo usuário</li></ul> |

Para obter uma lista de todas as mutações de contêiner no mesmo banco de dados, consulte o artigo [coleções de MongoDB restauráveis-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Recursos restauráveis do MongoDB

Cada recurso representa um banco de dados individual e todas as coleções nesse banco de dados.

|Nome da propriedade |Descrição  |
|---------|---------|
| databaseName  |O nome do banco de dados MongoDB. |
| coleções de | A lista de coleções do MongoDB neste banco de dados. |

Para obter uma lista de todas as combinações de banco de dados e coleção do MongoDB existentes na conta no carimbo de data e hora e no local fornecidos, consulte o artigo [recursos do MongoDB restauráveis – lista](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbresources/list) .

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
