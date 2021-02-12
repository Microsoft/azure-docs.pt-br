---
title: Tutorial – Backup do BD SAP HANA no Azure usando a CLI do Azure
description: Neste tutorial, saiba como fazer backup de bancos de dados SAP HANA executados em uma VM do Azure em um cofre dos Serviços de Recuperação do Backup do Azure usando a CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f146bed9ee607fe1b1b6062f9fe372fbb9b9ba6a
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746738"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: backup de bancos de dados SAP HANA em uma VM do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure por meio da linha de comando ou por meio de scripts. Esta documentação fornece detalhes de como fazer backup de um banco de dados SAP HANA e como disparar backups sob demanda e tudo isso com o uso da CLI do Azure. Você também pode executar essas etapas usando o [portal do Azure](./backup-azure-sap-hana-database.md).

Este documento pressupõe que você já tem um banco de dados SAP HANA instalado em uma VM do Azure. (Você também pode [criar uma VM usando a CLI do Azure](../virtual-machines/linux/quick-create-cli.md)). Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação
> * Registrar uma instância do SAP HANA e descobrir banco(s) de dados nela
> * Habilitar o backup em um banco de dados SAP HANA
> * Disparar um backup sob demanda

Confira os [cenários atualmente compatíveis](./sap-hana-backup-support-matrix.md#scenario-support) com SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.30 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é um contêiner lógico que armazena os dados de backup para cada recurso protegido, como VMs do Azure ou cargas de trabalho em execução em VMs do Azure, como bancos de dados SQL ou HANA. Quando o trabalho de backup para um recurso protegido é executado, ele cria um ponto de recuperação no cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Especifique o mesmo grupo de recursos e o local da VM que você deseja proteger. Saiba como criar uma VM usando a CLI do Azure com este [início rápido de VM](../virtual-machines/linux/quick-create-cli.md).

Para este tutorial, usaremos o seguinte:

* um grupo de recursos chamado *saphanaResourceGroup*
* uma VM chamada *saphanaVM*
* recursos no local *westus2*.

Vamos criar um cofre chamado *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Por padrão, o cofre dos Serviços de Recuperação é definido para o armazenamento com redundância geográfica. O armazenamento com redundância geográfica verifica se os dados de backup são replicados para uma região secundária do Azure a centenas de quilômetros de distância da região primária. Se a configuração de redundância de armazenamento precisar ser modificada, use o cmdlet [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set).

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Para ver se o cofre foi criado com êxito, use o cmdlet [az backup vault list](/cli/azure/backup/vault#az-backup-vault-list). Você verá a seguinte resposta:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrar e proteger a instância do SAP HANA

Para que a instância do SAP HANA (a VM com SAP HANA instalado) seja descoberta pelos serviços do Azure, um [script de pré-registro](https://aka.ms/scriptforpermsonhana) deve ser executado no computador SAP HANA. Atenda a todos os [pré-requisitos](./tutorial-backup-sap-hana-db.md#prerequisites) antes de executar o script. Para saber mais sobre o que o script faz, confira a seção [O que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

Depois que o script é executado, a instância do SAP HANA pode ser registrada com o cofre dos Serviços de Recuperação que criamos anteriormente. Para registrar a instância, use o cmdlet [az backup container register](/cli/azure/backup/container#az-backup-container-register). *VMResourceId* é a ID de recurso da VM que você criou para instalar o SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Se a VM não estiver no mesmo grupo de recursos do cofre, o *saphanaResourceGroup* vai se referir ao grupo de recursos em que o cofre foi criado.

O registro da instância do SAP HANA descobre automaticamente todos os seus bancos de dados atuais. No entanto, para descobrir novos bancos de dados que possam ser adicionados no futuro, consulte a seção [Descobrindo novos bancos de dados adicionados à instância registrada do SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance).

Para verificar se a instância do SAP HANA foi registrada com êxito no cofre, use o cmdlet [az backup container list](/cli/azure/backup/container#az-backup-container-list). Você verá a seguinte resposta:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A coluna "nome" na saída acima refere-se ao nome do contêiner. Esse nome de contêiner será usado nas próximas seções para habilitar backups e dispará-los. Que, nesse caso, é *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Habilitar backup no banco de dados SAP HANA

O cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) lista todos os bancos de dados descobertos na instância do SAP HANA que você registrou na etapa anterior.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Você deve encontrar o banco de dados do qual deseja fazer backup nesta lista, que terá a seguinte aparência:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Como você pode ver na saída acima, o SID do sistema SAP HANA é HXE. Neste tutorial, configuraremos o backup para o banco de dados *saphanadatabase;hxe;hxe* que reside no servidor *hxehost*.

Para proteger e configurar o backup em um banco de dados, um de cada vez, usamos o cmdlet [az backup protection enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl). Forneça o nome da política que você deseja usar. Para criar uma política usando a CLI, use o cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create). Para este tutorial, vamos usar a política *sapahanaPolicy*.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Você pode verificar se a configuração de backup acima está concluída usando o cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list). A saída será exibida da seguinte maneira:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

O cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list) lista todos os trabalhos de backup (agendados ou sob demanda) que foram executados ou estão atualmente em execução no banco de dados protegido, além de outras operações como registrar, configurar backup e excluir dados de backup.

>[!NOTE]
>O Backup do Azure não é ajustado automaticamente para alterações do horário de verão ao fazer backup de um banco de dados SAP HANA em execução em uma VM do Azure.
>
>Modifique a política manualmente, se necessário.

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Enquanto a seção acima detalha como configurar um backup agendado, esta seção fala sobre o disparo de um backup sob demanda. Para fazer isso, usamos o cmdlet [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now).

>[!NOTE]
> A política de retenção de um backup sob demanda é determinada pela política de retenção subjacente do banco de dados.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A saída será exibida da seguinte maneira:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

>[!NOTE]
>Além do agendamento de um backup completo ou diferencial, eles também podem ser disparados manualmente. Os backups de log são automaticamente disparados e gerenciados pelo SAP HANA internamente.
>
> Atualmente não há suporte para backups incrementais no Backup do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber como restaurar um banco de dados SAP HANA em uma VM do Azure usando a CLI, prossiga para o tutorial: [Restaurar um banco de dados SAP HANA na VM do Azure usando a CLI](tutorial-sap-hana-restore-cli.md)

* Para saber como fazer backup de um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, consulte [Fazer backup de um banco de dados SAP HANA em VMs do Azure](./backup-azure-sap-hana-database.md)
