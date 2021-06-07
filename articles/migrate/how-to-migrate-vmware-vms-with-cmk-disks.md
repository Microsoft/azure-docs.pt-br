---
title: Migrar máquinas virtuais VMware para o Azure com a SSE (criptografia do lado do servidor) e CMK (chaves gerenciadas pelo cliente) usando a migração de servidor de migrações para Azure
description: Saiba como migrar VMs VMware para o Azure com criptografia do lado do servidor (SSE) e chaves gerenciadas pelo cliente (CMK) usando a migração de servidor de migrações para Azure
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751080"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrar VMs VMware para VMs do Azure habilitadas com criptografia do lado do servidor e chaves gerenciadas pelo cliente

Este artigo descreve como migrar VMs VMware para máquinas virtuais do Azure com discos criptografados usando a SSE (criptografia do lado do servidor) com chaves gerenciadas pelo cliente (CMK), usando a migração de servidor de migrações para Azure (replicação sem agente).

A experiência do portal de migração do servidor de migrações para Azure permite [migrar VMs VMware para o Azure com replicação sem agente.](tutorial-migrate-vmware.md) A experiência do portal atualmente não oferece a capacidade de ligar o SSE com CMK para seus discos replicados no Azure. A capacidade de ativar o SSE com CMK para os discos replicados está disponível no momento apenas por meio da API REST. Neste artigo, você verá como criar e implantar um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) para replicar uma VM VMware e configurar os discos replicados no Azure para usar o SSE com CMK.

Os exemplos neste artigo usam [Azure PowerShell](/powershell/azure/new-azureps-module-az) para executar as tarefas necessárias para criar e implantar o modelo do Resource Manager.

[Saiba mais](../virtual-machines/disk-encryption.md) sobre criptografia do lado do servidor (SSE) com CMK (chaves gerenciadas pelo cliente) para discos gerenciados.

## <a name="prerequisites"></a>Pré-requisitos

- [Examine o tutorial](tutorial-migrate-vmware.md) sobre migração de VMs VMware para o Azure com replicação sem agente para entender os requisitos da ferramenta.
- [Siga estas instruções](./create-manage-projects.md) para criar um projeto de migrações para Azure e adicionar a ferramenta **migrações para Azure: Server Migration** para o projeto.
- [Siga estas instruções](how-to-set-up-appliance-vmware.md) para configurar o dispositivo de migração do Azure para VMware em seu ambiente local e concluir a descoberta.

## <a name="prepare-for-replication"></a>Preparar a replicação

Depois que a descoberta de VM for concluída, a linha servidores descobertos no bloco migração de servidor mostrará uma contagem de VMs VMware descobertas pelo dispositivo.

Antes que você possa iniciar a replicação de VMs, a infraestrutura de replicação precisa ser preparada.

1. Crie uma instância do barramento de serviço na região de destino. O barramento de serviço é usado pelo dispositivo de migrações do Azure local para se comunicar com o serviço de migração de servidor para coordenar a replicação e a migração.
2. Crie uma conta de armazenamento para transferência de logs de operação da replicação.
3. Crie uma conta de armazenamento para a qual o dispositivo de migração do Azure carrega os dados de replicação.
4. Crie um Key Vault e configure o Key Vault para gerenciar tokens de assinatura de acesso compartilhado para acesso de blob nas contas de armazenamento criadas na etapa 3 e 4.
5. Gere um token de assinatura de acesso compartilhado para o barramento de serviço criado na etapa 1 e crie um segredo para o token no Key Vault criado na etapa anterior.
6. Crie uma política de acesso de Key Vault para fornecer ao dispositivo de migrações do Azure local (usando o aplicativo AAD do dispositivo) e o serviço de migração do servidor acesse o Key Vault.
7. Crie uma política de replicação e configure o serviço de migração de servidor com detalhes da infraestrutura de replicação criada na etapa anterior.

A infraestrutura de replicação deve ser criada na região do Azure de destino para a migração e na assinatura do Azure de destino para a qual as VMs estão sendo migradas.

A experiência do portal de migração do servidor simplifica a preparação da infraestrutura de replicação fazendo isso automaticamente quando você replica uma VM pela primeira vez em um projeto. Neste artigo, vamos supor que você já tenha replicado uma ou mais VMs usando a experiência do portal e que a infraestrutura de replicação já esteja criada. Veremos como descobrir detalhes da infraestrutura de replicação existente e como usar esses detalhes como entradas para o modelo do Resource Manager que será usado para configurar a replicação com o CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identificando componentes da infraestrutura de replicação

1. Na portal do Azure, acesse a página grupos de recursos e selecione o grupo de recursos no qual o projeto de migração do Azure foi criado.
2. Selecione **implantações** no menu à esquerda e procure um nome de implantação começando com a cadeia de caracteres *"Microsoft. MigrateV2. VMwareV2EnableMigrate"*. Você verá uma lista de modelos do Resource Manager criados pela experiência do portal para configurar a replicação para VMs neste projeto. Vamos baixar um desses modelos e usá-lo como base para preparar o modelo para replicação com o CMK.
3. Para baixar o modelo, selecione qualquer implantação que corresponda ao padrão de cadeia de caracteres na etapa anterior > selecione **modelo** no menu à esquerda > clique em **baixar** no menu superior. Salve o template.jsno arquivo localmente. Você editará esse arquivo de modelo na última etapa.

## <a name="create-a-disk-encryption-set"></a>Criar um conjunto de criptografia de disco

Um objeto de conjunto de criptografia de disco mapeia o Managed Disks para um Key Vault que contém a CMK a ser usada para a SSE. Para replicar VMs com CMK, você criará um conjunto de criptografia de disco e o passará como uma entrada para a operação de replicação.

Siga o exemplo [aqui](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) para criar um conjunto de criptografia de disco usando Azure PowerShell. Verifique se o conjunto de criptografia de disco foi criado na assinatura de destino para a qual as VMs estão sendo migradas e na região do Azure de destino para a migração.

O conjunto de criptografia de disco pode ser configurado para criptografar discos gerenciados com uma chave gerenciada pelo cliente ou para criptografia dupla com uma chave gerenciada pelo cliente e uma chave de plataforma. Para usar a opção criptografia dupla em repouso, configure o conjunto de criptografia de disco conforme descrito [aqui](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

No exemplo mostrado abaixo, o conjunto de criptografia de disco está configurado para usar uma chave gerenciada pelo cliente.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Obter detalhes da VM do VMware para migrar

Nesta etapa, você usará Azure PowerShell para obter os detalhes da VM que precisa ser migrada. Esses detalhes serão usados para construir o modelo do Resource Manager para replicação. Especificamente, as duas propriedades de interesse são:

- A ID de recurso da máquina para as VMs descobertas.
- A lista de discos para a VM e seus identificadores de disco.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Copie o valor da cadeia de caracteres de SiteId correspondente ao dispositivo de migrações para Azure do qual a VM é descoberta. No exemplo mostrado acima, o SiteId é *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Copie os valores de UUID de ResourceId, nome e disco para o computador a ser migrado.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Criar modelo do Resource Manager para replicação

- Abra o arquivo de modelo do Resource Manager que você baixou na etapa **identificando os componentes da infraestrutura de replicação** em um editor de sua escolha.
- Remova todas as definições de recurso do modelo, exceto os recursos que são do tipo *"Microsoft. recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Se houver várias definições de recursos do tipo acima, remova todos, exceto um. Remova as definições de propriedade **dependentes** da definição de recurso.
- No final desta etapa, você deve ter um arquivo parecido com o exemplo abaixo e tem o mesmo conjunto de propriedades.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Edite a propriedade **Name** na definição de recurso. Substitua a cadeia de caracteres que segue o último "/" na propriedade Name com o valor de *$Machine. Nome*(da etapa anterior).
- Altere o valor da propriedade **Properties. providerSpecificDetails. vmwareMachineId** com o valor de *$Machine. ResourceId*(da etapa anterior).
- Defina os valores para **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** para a ID do grupo de recursos de destino, a ID de recurso de rede virtual de destino e o nome da sub-rede de destino, respectivamente.
- Defina o valor de **LicenseType** como "WindowsServer" para aplicar benefício híbrido do Azure para essa VM. Se essa VM não estiver qualificada para Benefício Híbrido do Azure, defina o valor de **LicenseType** como nolicenciadotype.
- Altere o valor da propriedade **targetVmName** para o nome da máquina virtual do Azure desejada para a VM migrada.
- Opcionalmente, adicione uma propriedade chamada **targetVmSize** abaixo da propriedade **targetVmName** . Defina o valor da propriedade **targetVmSize** para o tamanho de máquina virtual do Azure desejado para a VM migrada.
- A propriedade **disksToInclude** é uma lista de entradas de disco para replicação com cada item de lista que representa um disco local. Crie quantos itens de lista forem o número de discos na VM local. Substitua a propriedade **DiskId** no item de lista para o UUID dos discos identificados na etapa anterior. Defina o valor de **isOSDisk** como "true" para o disco do sistema operacional da VM e "false" para todos os outros discos. Deixe as propriedades **logStorageAccountId** e **logStorageAccountSasSecretName** inalteradas. Defina o valor de **disktype** como o tipo de disco gerenciado do Azure (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) a ser usado para o disco. Para os discos que precisam ser criptografados com CMK, adicione uma propriedade chamada **diskEncryptionSetId** e defina o valor para a ID de recurso do conjunto de criptografia de disco criado (**$des. ID**) na etapa *criar um conjunto de criptografia de disco*
- Salve o arquivo de modelo editado. Para o exemplo acima, o arquivo de modelo editado tem a seguinte aparência:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Configurar a replicação

Agora você pode implantar o modelo editado do Resource Manager no grupo de recursos do projeto para configurar a replicação para a VM. Saiba como [implantar recursos com modelos de Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Próximas etapas

[Monitore](tutorial-migrate-vmware.md#track-and-monitor) o status de replicação por meio da experiência do portal e execute migrações e migração de teste.