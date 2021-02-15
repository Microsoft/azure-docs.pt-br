---
title: Criar políticas de backup usando a API REST
description: Neste artigo, você aprenderá a criar e gerenciar políticas de backup (agendamento e retenção) usando a API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179599"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Criar políticas de backup de serviços de recuperação do Azure usando a API REST

As etapas para criar uma política de backup para uma área segura do Azure Recovery Services são descritas no [documento da API REST da política](/rest/api/backup/protectionpolicies/createorupdate). Vamos usar este documento como uma referência para criar uma política para o backup de VM do Azure.

## <a name="create-or-update-a-policy"></a>Criar ou atualizar uma política

Para criar ou atualizar uma política do Backup do Azure, use a seguinte operação *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

O `{policyName}` e `{vaultName}` são fornecidas no URI. Informações adicionais são fornecidas no corpo da solicitação.

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

Por exemplo, para criar uma política para o backup de VM do Azure, a seguir estão os componentes do corpo da solicitação.

|Nome  |Obrigatório  |Type  |Descrição  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Propriedades de ProtectionPolicyResource        |
|marcas     |         | Objeto        |  Marcações de recursos       |

Para obter a lista completa de definições no corpo da solicitação, consulte o [documento da API REST da política de backup](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Exemplo do corpo de solicitação

O corpo da solicitação a seguir define uma política de backup para backups de VM do Azure.

A diretiva diz:

- Faça um backup semanal todas as segundas, quartas e quintas-feiras, às 10h, horário padrão do Pacífico.
- Guarde os backups realizados todas as segundas, quartas e quintas-feiras durante uma semana.
- Manter os backups feitos em cada primeiro quarta-feira e quinta-feira terceiro de um mês de dois meses (substituições retenção condições anteriores, se houver).
- Guarde os backups realizados na quarta segunda e quarta quinta-feira, em fevereiro e novembro, por quatro anos (substitui as condições de retenção anteriores, se houver).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Os formatos de hora para programação e retenção suportam apenas o DateTime. Eles não dão suporte apenas ao formato de hora.

## <a name="responses"></a>Respostas

A criação/atualização de política de backup é um [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [Proteção PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Aceito     |         |     Aceito    |

### <a name="example-responses"></a>Respostas de exemplo

Depois de enviar a solicitação *PUT* para criação ou atualização de política, a resposta inicial é 202 (Aceito) com um cabeçalho de local ou cabeçalho assíncrono do Azure.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou o cabeçalho Azure-AsyncOperation com um simples comando *GET*.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Depois que a operação for concluída, ele retorna 200 (OK) com o conteúdo de política no corpo da resposta.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Se uma política já estiver sendo usada para proteger um item, qualquer atualização na política resultará em [modificar a proteção](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) para todos os itens associados.

## <a name="next-steps"></a>Próximas etapas

[Habilitar a proteção para uma VM do Azure desprotegidos](backup-azure-arm-userestapi-backupazurevms.md).

Para obter mais informações sobre as APIs REST do Backup do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
