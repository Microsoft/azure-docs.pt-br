---
title: Atualizar a política de backup de VM existente usando a CLI
description: Saiba como atualizar a política de backup de VM existente usando CLI do Azure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728571"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Atualizar a política de backup de VM existente usando a CLI

Você pode usar CLI do Azure para atualizar uma política de backup de VM existente. Este artigo explicará como exportar a política existente para um arquivo JSON, modificar o arquivo e, em seguida, usar CLI do Azure para atualizar a política com a política modificada.

## <a name="modify-an-existing-policy"></a>Modificar uma política existente

Para modificar uma política de backup de VM existente, siga estas etapas:

1. Execute o comando [AZ backup Policy show](/cli/azure/backup/policy#az_backup_policy_show) para recuperar os detalhes da política que você deseja atualizar.

    Exemplo:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    O exemplo acima mostra os detalhes de uma política de VM com o nome *testing123*.

    Saída:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Salve a saída acima em um arquivo. JSON. Por exemplo, vamos salvá-lo como *Policy.jsem*.
1. Atualize o arquivo JSON com base em seus requisitos e salve as alterações.

    Exemplo: para atualizar a retenção semanal para 60 dias, atualize a seção a seguir do arquivo JSON alterando a contagem para 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Salve as alterações.
1. Execute o comando [AZ backup Policy Set](/cli/azure/backup/policy#az_backup_policy_set) e passe o caminho completo do arquivo JSON atualizado como o valor para o parâmetro **--Policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Você também pode recuperar a política JSON de exemplo executando o comando [AZ backup Policy Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) .

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar backups de VM do Azure com o serviço de backup do Azure](backup-azure-manage-vms.md)