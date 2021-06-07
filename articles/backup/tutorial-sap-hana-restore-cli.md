---
title: 'Tutorial: Restauração do BD SAP HANA no Azure usando a CLI'
description: Neste tutorial, saiba como restaurar bancos de dados SAP HANA executados em uma VM do Azure por meio de um cofre dos Serviços de Recuperação do Backup do Azure usando a CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a249ab63aa72c1d39ab1626e72ff3b2037f3f723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768443"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Restaurar bancos de dados SAP HANA em uma VM do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure na linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como restaurar um banco de dados SAP HANA copiado em backup em uma VM do Azure usando a CLI do Azure. Execute também estas etapas usando o [portal do Azure](./sap-hana-db-restore.md).

Use o [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos da CLI.

Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Exibir pontos de restauração de um banco de dados copiado em backup
> * Restaurar um banco de dados

Este tutorial pressupõe que você tenha um banco de dados SAP HANA em execução em uma VM do Azure cujo backup tenha sido feito com o Backup do Azure. Se você usou [Fazer backup de um banco de dados SAP HANA no Azure usando a CLI](tutorial-sap-hana-backup-cli.md) para fazer backup do banco de dados SAP HANA, você está usando os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* um contêiner protegido chamado *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* um banco de dados/um item submetido a backup chamado *saphanadatabase;hxe;hxe*
* recursos na região *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Exibir pontos de restauração de um banco de dados copiado em backup

Para ver a lista de todos os pontos de recuperação de um banco de dados, use o cmdlet [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) da seguinte maneira:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A lista de pontos de recuperação terá a seguinte aparência:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Como você pode ver, a lista acima contém três pontos de recuperação: um para o backup completo, um para o diferencial e um para o de log.

>[!NOTE]
>Veja também os pontos inicial e final de cada cadeia de backup de log ininterrupta usando o cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain).

## <a name="prerequisites-to-restore-a-database"></a>Pré-requisitos para restaurar um banco de dados

Garanta que os seguintes pré-requisitos sejam atendidos antes de restaurar um banco de dados:

* Você pode restaurar o banco de dados somente em uma instância do SAP HANA que esteja na mesma região
* A instância de destino precisa ser registrada no mesmo cofre da origem
* O Backup do Azure não pode identificar duas instâncias diferentes do SAP HANA na mesma VM. Portanto, a restauração de dados de uma instância para outra na mesma VM não é possível.

## <a name="restore-a-database"></a>Restaurar um banco de dados

O Backup do Azure pode restaurar bancos de dados SAP HANA que estão em execução em VMs do Azure da seguinte maneira:

* Restauração em uma data ou uma hora específica (com precisão de segundos) usando backups de log. O Backup do Azure determina automaticamente os backups diferenciais completos apropriados e a cadeia de backups de log necessários para a restauração com base na hora selecionada.
* Restauração em um backup completo ou diferencial específico para restauração em um ponto de recuperação específico.

Para restaurar um banco de dados, use o cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl), que exige um objeto de configuração de recuperação como uma das entradas. Esse objeto pode ser gerado com o cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show). O objeto de configuração de recuperação contém todos os detalhes para executar uma restauração. Um deles é o modo de restauração: **OriginalWorkloadRestore** ou **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore**: restaure os dados para a mesma instância do SAP HANA da fonte original. Essa opção substitui o banco de dados original. <br>
> **AlternateWorkloadRestore**: restaure o banco de dados em uma localização alternativa e mantenha o banco de dados de origem.

## <a name="restore-to-alternate-location"></a>Restauração em uma localização alternativa

Para restaurar um banco de dados em uma localização alternativa, use **AlternateWorkloadRestore** como o modo de restauração. Em seguida, você precisará escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou um dos pontos de restauração anteriores.

Neste tutorial, você fará a restauração em um ponto de restauração anterior. [Veja a lista de pontos de restauração](#view-restore-points-for-a-backed-up-database) do banco de dados e escolha o ponto no qual deseja fazer a restauração. Este tutorial usará o ponto de restauração com o nome *7660777527047692711*.

Usando o nome do ponto de restauração acima e o modo de restauração, vamos criar o objeto de configuração de recuperação usando o cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show). Vejamos o que significa cada um dos parâmetros restantes nesse cmdlet:

* **--target-item-name** Esse é o nome que será usado pelo banco de dados restaurado. Nesse caso, usamos o nome *restored_database*.
* **--target-server-name** Esse é o nome de um servidor do SAP HANA registrado com êxito em um cofre dos Serviços de Recuperação e que reside na mesma região do banco de dados a ser restaurado. Neste tutorial, restauraremos o banco de dados no mesmo servidor do SAP HANA que protegemos, chamado *hxehost*.
* **--target-server-type** Para a restauração de bancos de dados SAP HANA, **HANAInstance** precisa ser usado.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante a este:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Agora, para restaurar o banco de dados, execute o cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl). Para usar esse comando, entraremos na saída JSON acima que está salva em um arquivo chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída se parecerá com esta:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="restore-and-overwrite"></a>Restaurar e substituir

Para fazer a restauração na localização original, usaremos **OriginalWorkloadRestore** como o modo de restauração. Em seguida, você precisará escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou um dos pontos de restauração anteriores.

Neste tutorial, escolheremos o ponto no tempo anterior “28-11-2019-09:53:00” para o qual será feita a restauração. Você pode fornecer esse ponto de restauração nos seguintes formatos: dd-mm-aaaa, dd-mm-aaaa-hh:mm:ss. Para escolher um ponto no tempo válido para a restauração, use o cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain), que lista os intervalos de backups da cadeia de logs ininterrupta.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante ao seguinte:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Agora, para restaurar o banco de dados, execute o cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl). Para usar esse comando, entraremos na saída JSON acima que está salva em um arquivo chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída se parecerá com esta:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos em vez de um banco de dados, usaremos **RestoreAsFiles** como o modo de restauração. Então escolha o ponto de restauração, que pode ser um ponto anterior no tempo ou um dos pontos de restauração anteriores. Depois que os arquivos são despejados em um caminho especificado, você pode pegar esses arquivos em qualquer computador SAP HANA em que queira restaurá-los como um banco de dados. Como você pode mover esses arquivos para qualquer computador, agora é possível restaurar os dados entre assinaturas e regiões.

Para este tutorial, escolheremos o ponto no tempo `28-11-2019-09:53:00` anterior para o qual restaurar e a localização para despejar arquivos de backup como `/home/saphana/restoreasfiles` no mesmo servidor SAP HANA. Você pode fornecer esse ponto de restauração em qualquer um dos seguintes formatos: **dd-mm-aaaa** ou **dd-mm-aaaa-hh:mm:ss**. Para escolher um ponto no tempo válido para a restauração, use o cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain), que lista os intervalos de backups da cadeia de logs ininterrupta.

Usando o nome do ponto de restauração acima e o modo de restauração, vamos criar o objeto de configuração de recuperação usando o cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show). Vejamos o que significa cada um dos parâmetros restantes nesse cmdlet:

* **--target-container-name** Esse é o nome de um servidor do SAP HANA registrado com êxito em um cofre dos Serviços de Recuperação e que reside na mesma região do banco de dados a ser restaurado. Neste tutorial, restauraremos o banco de dados como arquivos no mesmo servidor do SAP HANA que protegemos, chamado *hxehost*.
* **-rp-name** para uma restauração de ponto no tempo, o nome do ponto de restauração será **DefaultRangeRecoveryPoint**

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante ao seguinte:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Agora, para restaurar o banco de dados como arquivos, execute o cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl). Para usar esse comando, vamos inserir a saída json acima, que é salva em um arquivo chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

A saída se parecerá com esta:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

Os arquivos despejados no contêiner de destino são:

* Arquivos de backup de banco de dados
* Arquivos de catálogo
* Arquivos de metadados JSON (para cada arquivo de backup envolvido)

Normalmente, um caminho de compartilhamento de rede (ou caminho de um compartilhamento de arquivo do Azure montado quando especificado como o caminho de destino) permite o acesso mais fácil a esses arquivos por outros computadores na mesma rede ou com o mesmo compartilhamento de arquivo do Azure montado neles.

>[!NOTE]
>Para restaurar os arquivos de backup de banco de dados em um compartilhamento de arquivo do Azure montado na VM registrada de destino, verifique se a conta raiz tem permissões de leitura/gravação no compartilhamento de arquivo do Azure.

Com base no tipo de ponto de restauração escolhido (**Ponto no tempo** ou **Completo e Diferencial**), você verá uma ou mais pastas criadas no caminho de destino. Uma das pastas chamada `Data_<date and time of restore>` contém os backups completo e diferencial e a outra pasta chamada `Log` contém os backups de log.

Mova esses arquivos restaurados para o servidor de SAP HANA no qual você deseja restaurá-los como um banco de dados. Em seguida, siga estas etapas para restaurar o banco de dados:

1. Defina permissões na pasta/diretório em que os arquivos de backup são armazenados usando o seguinte comando:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Execute o próximo conjunto de comandos como `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Gere o arquivo de catálogo para restauração. Extraia o **BackupId** do arquivo de metadados JSON para o backup completo, que será usado posteriormente na operação de restauração. Verifique se os backups completo e de log estão em pastas diferentes e exclua os arquivos de catálogo e os arquivos de metadados JSON nessas pastas.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    No comando acima:

    * `<DataFileDir>` – a pasta que contém os backups completos
    * `<LogFilesDir>` – a pasta que contém os backups de log
    * `<PathToPlaceCatalogFile>` – a pasta na qual o arquivo de catálogo gerado deve ser colocado

1. Restaure usando o arquivo de catálogo recém-gerado por meio do HANA Studio ou execute a consulta de restauração do HDBSQL com esse catálogo que acaba de ser gerado. As consultas HDBSQL estão listadas abaixo:

    * Para restaurar para um momento determinado:

        Se você estiver criando um banco de dados restaurado, execute o comando HDBSQL para criar um banco de dados `<DatabaseName>` e interrompa o banco de dados para restauração. No entanto, se você estiver restaurando apenas um banco de dados existente, execute o comando HDBSQL para interromper o banco de dados.

        Em seguida, execute o seguinte comando para restaurar o banco de dados:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – nome do novo banco de dados ou banco de dados existente que você deseja restaurar
        * `<Timestamp>` – carimbo de data/hora exato da restauração pontual
        * `<DatabaseName@HostName>` – nome do banco de dados cujo backup é usado para restauração e o nome do servidor de **host**/SAP HANA em que esse banco de dados reside. A opção `USING SOURCE <DatabaseName@HostName>` especifica que o backup de dados (usado para restauração) é de um banco de dados com um SID ou nome diferente do computador de SAP HANA de destino. Portanto, isso não precisa ser especificado para restaurações feitas no mesmo servidor HANA do qual o backup é feito.
        * `<PathToGeneratedCatalogInStep3>` – caminho para o arquivo de catálogo gerado na **Etapa 3**
        * `<DataFileDir>` – a pasta que contém os backups completos
        * `<LogFilesDir>` – a pasta que contém os backups de log
        * `<BackupIdFromJsonFile>` – o **BackupId** extraído na **Etapa 3**

    * Para restaurar para um backup completo ou diferencial específico:

        Se você estiver criando um banco de dados restaurado, execute o comando HDBSQL para criar um banco de dados `<DatabaseName>` e interrompa o banco de dados para restauração. No entanto, se você estiver restaurando apenas um banco de dados existente, execute o comando HDBSQL para interromper o banco de dados:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – o nome do novo banco de dados ou banco de dados existente que você deseja restaurar
        * `<Timestamp>` – o carimbo de data/hora exato da restauração pontual
        * `<DatabaseName@HostName>` – o nome do banco de dados cujo backup é usado para restauração e o nome do servidor de **host**/SAP HANA em que esse banco de dados reside. A opção `USING SOURCE <DatabaseName@HostName>` especifica que o backup de dados (usado para restauração) é de um banco de dados com um SID ou nome diferente do computador de SAP HANA de destino. Portanto, ele não precisa ser especificado para restaurações feitas no mesmo servidor HANA do qual o backup é feito.
        * `<PathToGeneratedCatalogInStep3>` – o caminho para o arquivo de catálogo gerado na **Etapa 3**
        * `<DataFileDir>` – a pasta que contém os backups completos
        * `<LogFilesDir>` – a pasta que contém os backups de log
        * `<BackupIdFromJsonFile>` – o **BackupId** extraído na **Etapa 3**

## <a name="next-steps"></a>Próximas etapas

* Para saber como gerenciar bancos de dados SAP HANA que são copiados em backup usando a CLI do Azure, prossiga para o tutorial [Gerenciar um banco de dados SAP HANA em uma VM do Azure usando a CLI](tutorial-sap-hana-backup-cli.md)

* Para saber como restaurar um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, veja [Restaurar um banco de dados SAP HANA em VMs do Azure](./sap-hana-db-restore.md)
