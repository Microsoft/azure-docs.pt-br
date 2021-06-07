---
title: 'Tutorial: Gerenciar BD SAP HANA submetido a backup usando a CLI'
description: Neste tutorial, saiba como gerenciar bancos de dados SAP HANA submetidos a backup em execução em uma VM do Azure usando a CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7090701e3642fd9703737060e0876c8bbfc27994
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765171"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Gerenciar bancos de dados SAP HANA em uma VM do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure por meio da linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como gerenciar um banco de dados SAP HANA submetido a backup em uma VM do Azure usando a CLI do Azure. Você também pode executar essas etapas usando [o portal do Azure](./sap-hana-db-manage.md).

Use o [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos da CLI.

Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Monitorar tarefas de backup e restauração
> * Proteger novos bancos de dados adicionados a uma instância do SAP HANA
> * Alterar a política
> * Parar a proteção
> * Retomar proteção

Se você usou [Fazer backup de um banco de dados SAP HANA no Azure usando a CLI](tutorial-sap-hana-backup-cli.md) para fazer backup do banco de dados SAP HANA, você estará usando os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* um contêiner protegido chamado *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* um banco de dados/um item submetido a backup chamado *saphanadatabase;hxe;hxe*
* recursos na região *westus2*

A CLI do Azure facilita o gerenciamento de um banco de dados SAP HANA em execução em uma VM do Azure cujo backup é feito usando o Backup do Azure. Este tutorial detalha cada uma das operações de gerenciamento.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorar tarefas de backup e restauração

Para monitorar trabalhos concluídos ou em execução no momento (backup ou restauração), use o cmdlet [az backup job list](/cli/azure/backup/job#az_backup_job_list). A CLI também permite [suspender um trabalho atualmente em execução](/cli/azure/backup/job#az_backup_job_stop) ou [aguardar até que um trabalho seja concluído](/cli/azure/backup/job#az_backup_job_wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A saída será parecida com esta:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Alterar política

Para alterar a política subjacente à configuração de backup do SAP HANA, use o cmdlet [az backup policy set](/cli/azure/backup/policy#az_backup_policy_set). O parâmetro Name neste cmdlet refere-se ao item de backup cuja política queremos alterar. Para este tutorial, substituiremos a política de nosso banco de dados SAP HANA *saphanadatabase;hxe;hxe* por uma nova política *newsaphanaPolicy*. Novas políticas podem ser criadas usando o cmdlet [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create).

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

O resultado deve ser assim:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Criar política de backup incremental

Para criar uma política de backup incremental, execute o comando [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) com os seguintes parâmetros:

* **--backup-management-type** – Carga de trabalho do Azure
* **--workload-type** – SAPHana
* **--name** – o nome da política
* **--policy** – arquivo JSON com os detalhes adequados para agenda e retenção
* **--resource-group** – grupo de recursos do cofre
* **--vault-name** – nome do cofre

Exemplo:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

JSON de exemplo (sappolicy.json):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

Depois que a política for criada com êxito, a saída do comando exibirá o JSON da política que você passou como um parâmetro ao executar o comando.

Você pode modificar a seção a seguir da política para especificar a frequência de backup e a retenção desejadas para backups incrementais.

Por exemplo:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Exemplo:

Se quiser ter backups incrementais somente nos sábados e mantê-los por 60 dias, faça as seguintes alterações na política:

* Atualize a contagem **retentionDuration** para 60 dias
* Especifique somente sábado como **ScheduleRunDays**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Proteger novos bancos de dados adicionados a uma instância do SAP HANA

[O registro de uma instância do SAP HANA com um cofre dos Serviços de Recuperação](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) descobre automaticamente todos os bancos de dados nessa instância.

No entanto, nos casos em que novos bancos de dados são adicionados à instância do SAP HANA mais tarde, use o cmdlet [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az_backup_protectable_item_initialize). Esse cmdlet descobre os novos bancos de dados adicionados.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Em seguida, use o cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) para listar todos os bancos de dados que foram descobertos em sua instância do SAP HANA. No entanto, essa lista exclui os bancos de dados nos quais o backup já foi configurado. Depois que o banco de dados a ser submetido a backup for descoberto, consulte [Habilitar backup em banco de dados SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

O novo banco de dados do qual você deseja fazer backup aparecerá nessa lista e terá a seguinte aparência:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Interromper a proteção de um banco de dados SAP HANA

Você pode interromper a proteção de um banco de dados SAP HANA de algumas maneiras:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interromper todos os trabalhos de backup futuros e deixar os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre e toda a remoção será interrompida ao parar a proteção com os dados de retenção.
* Você será cobrado pela instância protegida e pelo armazenamento consumido.
* Se você excluir uma fonte de dados sem interromper os backups, os novos backups falharão.

Vamos examinar cada uma das maneiras de parar a proteção mais detalhadamente.

### <a name="stop-protection-with-retain-data"></a>Interrompa a proteção com retenção de dados

Para interromper a proteção com retenção de dados, use o cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

O resultado deve ser assim:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

### <a name="stop-protection-without-retain-data"></a>Interromper a proteção sem retenção de dados

Para interromper a proteção sem retenção de dados, use o cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

O resultado deve ser assim:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="resume-protection"></a>Retomar proteção

Quando você interrompe a proteção do banco de dados SAP HANA com retenção de dados, pode posteriormente continuar a proteção. Se você não mantiver os dados de backup, não será possível retomar a proteção.

Para retomar a proteção, use o cmdlet [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume).

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

O resultado deve ser assim:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="next-steps"></a>Próximas etapas

* Para saber como fazer backup de um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, consulte [Fazer backup de bancos de dados SAP HANA em VMs do Azure](./backup-azure-sap-hana-database.md)

* Para saber como gerenciar um banco de dados SAP HANA submetido a backup em execução na VM do Azure usando o portal do Azure, consulte [Gerenciar bancos de dados SAP HANA submetidos a backup em VM do Azure](./sap-hana-db-manage.md)