---
title: Automatize migrações VMware sem agente em migrações para Azure
description: Descreve como usar scripts para migrar um grande número de VMs VMware em migrações para Azure
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754259"
---
# <a name="scale-migration-of-vmware-vms"></a>Dimensionar a migração de VMs VMware 

Este artigo ajuda você a entender como usar scripts para migrar um grande número de VMs (máquinas virtuais) VMware usando o método sem agente. Para dimensionar as migrações, use o [módulo Azure Migrations PowerShell](./tutorial-migrate-vmware-powershell.md). 

Os scripts de automação de migração do VMware migram para o Azure estão disponíveis para download no repositório de [exemplos de Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) no github. Os scripts podem ser usados para migrar VMs do VMware para o Azure usando o método de migração sem agente. Os comandos do PowerShell para migrações para Azure usados nesses scripts estão documentados [aqui](./tutorial-migrate-vmware-powershell.md).

## <a name="current-limitations"></a>Limitações atuais
- Esses scripts dão suporte à migração de VMs VMware com todos os discos. Você pode atualizar os scripts se quiser replicar seletivamente os discos anexados a uma VM VMware. 
- Os scripts dão suporte ao uso de recomendações de avaliação. Se as recomendações de avaliação não forem usadas, todos os discos anexados à VM VMware serão migrados para o mesmo tipo de disco gerenciado (Standard ou Premium). Você pode atualizar os scripts se quiser usar vários tipos de discos gerenciados com a mesma VM

## <a name="prerequisites"></a>Pré-requisitos

- [Concluir o tutorial de descoberta](tutorial-discover-vmware.md) para preparar o Azure e o VMware para migração.
- Recomendamos que você conclua o segundo tutorial para [avaliar as VMs VMware](./tutorial-assess-vmware-azure-vm.md) antes de migrá-las para o Azure.
- Você precisa ter o módulo `Az` do Azure PowerShell. Se precisar instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Instalar o módulo do PowerShell das Migrações para Azure

O módulo do PowerShell das Migrações para Azure está disponível na versão prévia. Você precisará instalar o módulo do PowerShell usando o comando a seguir. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>Arquivo de entrada CSV
Depois que todos os pré-requisitos forem concluídos, você precisará criar um arquivo CSV que tenha dados para cada VM de origem que você deseja migrar. Todos os scripts são desenvolvidos para trabalhar no mesmo arquivo CSV. Um modelo de CSV de exemplo está disponível na pasta de scripts para sua referência. O arquivo CSV é configurável para que você possa usar as recomendações de avaliação e até mesmo especificar se determinadas operações não devem ser disparadas para uma VM específica. 

> [!NOTE]
> O mesmo arquivo CSV pode ser usado para migrar VMs em vários projetos de migrações para Azure.

### <a name="csv-file-schema"></a>Esquema de arquivo CSV

**Cabeçalho da coluna** | **Descrição**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Forneça a ID da assinatura do projeto de migrações para Azure.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Forneça o nome do grupo de recursos de migrações para Azure.
AZMIGRATEPROJECT_NAME | Forneça o nome do projeto de migrações para Azure no qual você deseja migrar servidores. 
SOURCE_MACHINE_NAME | Forneça o nome amigável (nome de exibição) para a VM descoberta no projeto de migrações para Azure.
AZMIGRATEASSESSMENT_NAME | Forneça o nome da avaliação que precisa ser aproveitada para a migração.
AZMIGRATEGROUP_NAME | Forneça o nome do grupo que foi usado para a avaliação de migrações para Azure.
TARGET_RESOURCE_GROUP_NAME | Forneça o nome do grupo de recursos do Azure para o qual a VM precisa ser migrada.
TARGET_VNET_NAME| Forneça o nome da rede virtual do Azure que a VM migrada deve usar.
TARGET_SUBNET_NAME | Forneça o nome da sub-rede na rede virtual de destino que a VM migrada deve usar. Se for deixado em branco, a sub-rede "default" será usada.
TARGET_MACHINE_NAME | Forneça o nome que a VM migrada deve usar no Azure. Se deixado em branco, o nome do computador de origem será usado.  
TARGET_MACHINE_SIZE | Forneça a SKU que a VM deve usar no Azure. Para migrar uma VM para D2_v2 VM no Azure, especifique o valor nesse campo como "Standard_D2_v2". Se você usar uma avaliação, esse valor será derivado com base na recomendação de avaliação.
LICENSE_TYPE | Especifique se deseja usar Benefício Híbrido do Azure para VMs do Windows Server. Use o valor "WindowsServer" para aproveitar Benefício Híbrido do Azure. Caso contrário, deixe em branco ou use "nolicensetype".
OS_DISK_ID | Forneça a ID do disco do sistema operacional para a VM a ser migrada. A ID do disco a ser usada é a propriedade do identificador exclusivo (UUID) para o disco recuperado usando o cmdlet Get-AzMigrateServer. O script usará o primeiro disco da VM como o disco do sistema operacional caso nenhum valor seja fornecido.
TARGET_DISKTYPE | Forneça o tipo de disco a ser usado para todos os discos da VM no Azure. Use ' Premium_LRS ' para discos gerenciados Premium, ' StandardSSD_LRS ' para discos SSD padrão e ' Standard_LRS ' para usar discos HDD padrão. Se você optar por usar uma avaliação, o script irá priorizar o uso de tipos de disco recomendados para cada disco da VM. Se você não usar a avaliação ou especificar qualquer valor, o script usará discos HDD padrão por padrão.    
AVAILABILITYZONE_NUMBER | Especifique o número da zona de disponibilidade a ser usado para a VM migrada. Você pode deixar isso em branco caso não deseje usar zonas de disponibilidade. 
AVAILABILITYSET_NAME | Especifique o nome do conjunto de disponibilidade a ser usado para a VM migrada. Você pode deixar isso em branco caso não queira usar o conjunto de disponibilidade.
TURNOFF_SOURCESERVER | Especifique ' Y ' se quiser desativar a VM de origem no momento da migração. Em caso contrário, use ' n'. Se for deixado em branco, o script assumirá o valor como ' n'.
TESTMIGRATE_VNET_NAME | Especifique o nome da rede virtual a ser usada para a migração de teste.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Se você quiser atualizar o grupo de recursos a ser usado pela VM migrada no Azure, especifique o nome do grupo de recursos do Azure, caso contrário, deixe em branco. 
UPDATED_TARGET_VNET_NAME | Se você quiser atualizar a rede virtual a ser usada pela VM migrada no Azure, especifique o nome da rede virtual do Azure, caso contrário, deixe em branco.
UPDATED_TARGET_MACHINE_NAME | Se você quiser atualizar o nome a ser usado pela VM migrada no Azure, especifique o novo nome a ser usado, caso contrário, deixe em branco.
UPDATED_TARGET_MACHINE_SIZE | Se você quiser atualizar a SKU a ser usada pela VM migrada no Azure, especifique a nova SKU a ser usada, caso contrário, deixe em branco.
UPDATED_AVAILABILITYZONE_NUMBER | Se você quiser atualizar a zona de disponibilidade a ser usada pela VM migrada no Azure, especifique a nova zona de disponibilidade a ser usada, caso contrário, deixe em branco.
UPDATED_AVAILABILITYSET_NAME | Se você quiser atualizar o conjunto de disponibilidade a ser usado pela VM migrada no Azure, especifique o novo conjunto de disponibilidade a ser usado, caso contrário, deixe em branco.
UPDATE_NIC1_ID | Especifique a ID da NIC a ser atualizada. Se deixado em branco, o script pressupõe que o valor seja a primeira NIC da VM descoberta. Se você não quiser atualizar a NIC da VM, deixe todos os campos que contenham o nome da NIC em branco. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Especifique o valor a ser usado para esta NIC. Use "primário", "secundário" ou "DoNotCreate" para especificar se essa NIC deve ser a primária, secundária ou não deve ser criada na VM migrada. Somente uma NIC pode ser especificada como NIC primária para a VM. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC1_SUBNET_NAME | Especifique o nome da sub-rede a ser usada para a NIC na VM migrada. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC1_IP | Especifique o endereço IPv4 a ser usado pela NIC na VM migrada se você quiser usar o IP estático. Use "auto" se desejar atribuir automaticamente o IP. Deixe em branco se não quiser atualizar.
UPDATE_NIC2_ID | Especifique a ID da NIC a ser atualizada. Se for deixado em branco, o script assumirá que o valor será a segunda NIC da VM descoberta. Se você não quiser atualizar a NIC da VM, deixe todos os campos que contenham o nome da NIC em branco.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Especifique o valor a ser usado para esta NIC. Use "primário", "secundário" ou "DoNotCreate" para especificar se essa NIC deve ser a primária, secundária ou não deve ser criada na VM migrada. Somente uma NIC pode ser especificada como NIC primária para a VM. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC2_SUBNET_NAME | Especifique o nome da sub-rede a ser usada para a NIC na VM migrada. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC2_IP | Especifique o endereço IPv4 a ser usado pela NIC na VM migrada se você quiser usar o IP estático. Use "auto" se desejar atribuir automaticamente o IP. Deixe em branco se não quiser atualizar.
OK_TO_UPDATE | Use ' Y ' para indicar se as propriedades da VM precisam ser atualizadas quando você executar o script de AzMigrate_UpdateMachineProperties. Use ' n' ou deixe em branco de outra forma.
OK_TO_MIGRATE | Use ' Y ' para indicar se a VM deve ser migrada quando você executar o script de AzMigrate_StartMigration. Use ' n' ou deixe em branco se não quiser migrar a VM. 
OK_TO_USE_ASSESSMENT | Use ' Y ' para indicar se a VM deve iniciar a replicação usando recomendações de avaliação quando você executar o script de AzMigrate_StartReplication. Isso substituirá os valores TARGET_MACHINE_SIZE e TARGET_DISKTYPE no arquivo CSV. Use ' n' ou deixe em branco se não quiser usar as recomendações de avaliação.
OK_TO_TESTMIGRATE | Use ' Y ' para indicar se a VM deve ser de teste migrada quando você executar o script de AzMigrate_StartTestMigration. Use ' n' ou deixe em branco se não quiser testar a migração da VM. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Use ' Y ' para indicar se o status de replicação da VM deve ser atualizado quando você executar o script de AzMigrate_ReplicationStatus. Use ' n' ou deixe em branco se não quiser atualizar o status de replicação.
OK_TO_CLEANUP | Use ' Y ' para indicar se a replicação da VM deve ser limpa quando você executar o script de AzMigrate_StopReplication. Use ' n' ou deixe em branco de outra forma.
OK_TO_TESTMIGRATE_CLEANUP | Use ' Y ' para indicar se a migração de teste para a VM deve ser limpa quando você executar o script de AzMigrate_CleanUpTestMigration. Use ' n' ou deixe em branco de outra forma.


## <a name="script-execution"></a>Execução do script

Quando o CSV estiver pronto, você poderá executar as etapas a seguir para migrar suas VMs VMware locais.

**Etapa nº** | **Nome do script** | **Descrição**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Habilitar a replicação para todas as VMs listadas no CSV, o script cria uma saída CSV e um arquivo de log para solução de problemas.
2 | AzMigrate_ReplicationStatus.ps1 | Verifique o status da replicação, o script cria uma saída CSV com o status de cada VM e um arquivo de log para solução de problemas.
3 | AzMigrate_UpdateMachineProperties.ps1 | Depois que as VMs tiverem concluído a replicação inicial, use esse script para atualizar as propriedades de destino da VM (Propriedades de computação e rede). O script cria uma saída CSV com os detalhes do trabalho para cada VM.
4 | AzMigrate_StartTestMigration.ps1 |  Inicie o failover de teste para todas as VMs listadas no CSV que estão configuradas para migração de teste. O script cria uma saída CSV com os detalhes do trabalho para cada VM.
5 | AzMigrate_CleanUpTestMigration.ps1 | Depois de validar manualmente as VMs que foram testadas com failover, use esse script para limpar as VMs de teste de cluster para todas as VMs listadas no CSV que estão configuradas para limpeza de migração de teste. O script cria uma saída CSV com os detalhes do trabalho para cada VM.
6 | AzMigrate_StartMigration.ps1 | Inicie a migração para todas as VMs listadas no CSV que estão configuradas para migração. O script cria uma saída CSV com os detalhes do trabalho para cada VM.
7 | AzMigrate_StopReplication.ps1 | Interrompe a replicação para a VM depois que ela é migrada com êxito ou se você deseja cancelar a replicação devido a outros motivos. O script cria uma saída CSV com os detalhes do trabalho para cada VM.


Os scripts a seguir são invocados por outros scripts para todas as operações de migrações para Azure, como habilitar a replicação, iniciar a migração de teste, atualizar as propriedades da VM e assim por diante. Verifique se todos os scripts estão presentes na mesma pasta/caminho. 

**Etapa nº** | **Nome do script** | **Descrição**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Script comum que contém funções para recuperar propriedades de avaliação (por meio da API), VMs descobertas e replicar VMs. 
2 | AzMigrate_CSV_Processor.ps1 | Script comum que contém funções usadas para operações de arquivo CSV, incluindo carregamento, leitura e impressão de logs. 
3 | AzMigrate_Logger.ps1 | Script comum invocado para gerar o arquivo de log para operações de automação de migração do Azure. O arquivo de log terá o formato log.Scriptname.Datetime.txt.

Além do acima, a pasta também contém AzMigrate_Template.ps1 que contém a estrutura estrutural para a criação de scripts personalizados para diferentes operações de migração do Azure. 

### <a name="script-execution-syntax"></a>Sintaxe de execução de script

Depois de baixar os scripts, os scripts podem ser executados da seguinte maneira.

Se você quiser executar o script para iniciar a replicação para VMs usando o arquivo Input.csv, use a sintaxe a seguir. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Para saber mais sobre como usar Azure PowerShell para migrar VMs VMware com migrações para Azure, siga o [tutorial](./tutorial-migrate-vmware-powershell.md).