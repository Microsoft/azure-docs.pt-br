---
title: Backup do Azure - Use o PowerShell para fazer backup de cargas de trabalho do DPM
description: Saiba como implantar e gerenciar o backup do Azure para o Data Protection Manager (DPM) usando o PowerShell
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: a269db3d97f23c16e848026ce78fc04c7a1182e8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465144"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implantar e gerenciar o backup do Azure para servidores do Data Protection Manager (DPM) usando o PowerShell

Este artigo mostra como usar o Azure PowerShell para configurar o Backup do Azure em um servidor DPM e para gerenciar backups e recuperações.

## <a name="setting-up-the-powershell-environment"></a>Configurando o ambiente do PowerShell

Antes de usar o PowerShell para gerenciar backups do Data Protection Manager para o Azure, você precisará ter o ambiente certo no PowerShell. No início da sessão do PowerShell, certifique-se de executar o comando a seguir para importar os módulos corretos e permitir que você faça referência corretamente aos cmdlets do DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Configuração e registro

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [Baixe o Azure PowerShell mais recente](/powershell/azure/install-az-ps).

As seguintes tarefas de configuração e de registro podem ser automatizadas com o PowerShell:

* Criar um cofre dos Serviços de Recuperação
* Instalando o agente de Backup do Azure
* Registrando-se no serviço de Backup do Azure
* Configurações de rede
* Configurações de criptografia

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de Backup.

1. Se você estiver usando um Backup do Azure pela primeira vez, deverá usar o cmdlet **Register-AzResourceProvider** para registrar o provedor do Serviço de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso do ARM e, portanto, você precisará colocá-lo em um Grupo de Recursos. Você pode usar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e o local para o grupo de recursos.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Use o cmdlet **New-AzRecoveryServicesVault** para criar um novo cofre. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especifique o tipo de redundância de armazenamento a usar. Você pode usar o [Armazenamento com Redundância Local (LRS)](../storage/common/storage-redundancy-lrs.md) ou o [Armazenamento com Redundância Geográfica (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra que a opção BackupStorageRedundancy para o testVault está definida como GeoRedundant.

   > [!TIP]
   > Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Use **Get-AzRecoveryServicesVault** para exibir a lista de todos os cofres na assinatura atual. Você pode usar esse comando para verificar se um novo cofre foi criado ou para ver quais cofres estão disponíveis na assinatura.

Execute o comando, Get-AzRecoveryServicesVault e todos os cofres na assinatura são listados.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalando o agente de Backup do Azure em um Servidor de DPM

Antes de instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a última versão do instalador no [Centro de Download da Microsoft](https://aka.ms/azurebackup_agent) ou na página Painel do cofre dos Serviços de Recuperação. Salve o instalador em um local de fácil acesso, como *C:\Downloads\*.

Para instalar o agente, execute o seguinte comando em um console do PowerShell com privilégios elevados **no servidor do DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção */nu* , a janela do **Windows Update** será aberta ao fim da instalação para verificar se há atualizações.

O agente será mostrado na lista de programas instalados. Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis por meio da linha de comando, use o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Padrão |
| --- | --- | --- |
| /q |Instalação silenciosa |- |
| /p:"local" |Caminho para a pasta de instalação para o agente de Backup do Azure. |C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent |
| /s:"local" |Caminho para a pasta de cache para o agente de Backup do Azure. |C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Aceitar o Microsoft Update |- |
| /nu |Não verificar se há atualizações após a conclusão da instalação |- |
| /d |Desinstala o agente dos Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço do host do proxy |- |
| /po |Número da porta do host do proxy |- |
| /pu |UserName do host do proxy |- |
| /pw |Senha do proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrar o DPM para um Cofre dos Serviços de Recuperação

Depois de criar o cofre dos Serviços de Recuperação, baixe o agente mais recente e as credenciais do cofre e armazene-os em um local conveniente como C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No servidor DPM, execute o cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar o computador no cofre.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Definições de configuração iniciais

Depois que o Servidor de DPM estiver registrado no cofre dos Serviços de Recuperação, ele será iniciado com as configurações de assinatura padrão. Essas configurações de assinatura incluem Rede, Criptografia e Área de preparo. Para alterar as configurações de assinatura, primeiro você precisa obter um identificador nas configurações existentes (padrão) usando o cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são feitas a este objeto local ```$setting``` do PowerShell e o objeto completo é confirmado no DPM e no Backup do Azure para salvá-los usando o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) . Você precisa usar o sinalizador ```–Commit``` para garantir que as alterações sejam mantidas. As configurações não serão aplicadas nem usadas pelo Backup do Azure, a menos que sejam confirmadas.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Rede

Se a conectividade do computador de DPM ao serviço de Backup do Azure na Internet é feita por meio de um servidor proxy, as configurações do servidor proxy devem ser fornecidas para backups bem-sucedidos. Isso é feito usando os parâmetros ```-ProxyServer``` e ```-ProxyPort```, ```-ProxyUsername``` e ```ProxyPassword``` com o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Neste exemplo, não há nenhum servidor proxy, por isso estamos explicitamente omitindo todas as informações relacionadas a proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

O uso de largura de banda também pode ser controlado com as opções de ```-WorkHourBandwidth``` e ```-NonWorkHourBandwidth``` para um determinado conjunto de dias da semana. Neste exemplo, não definimos nenhuma limitação.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurando a Área de preparo

O agente de Backup do Azure em execução no servidor DPM precisa armazenar temporariamente os dados restaurados da nuvem (área de preparação local). Configure a área de preparo usando o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) e o parâmetro ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de preparo será definida como *C:\StagingArea* no objeto do PowerShell```$setting```. Verifique se a pasta especificada já existe, ou a confirmação final das configurações de assinatura falhará.

### <a name="encryption-settings"></a>Configurações de criptografia

Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração. É importante manter essas informações seguras depois de defini-las.

No exemplo abaixo, o primeiro comando converte a cadeia de caracteres ```passphrase123456789``` em uma cadeia de caracteres segura e atribui a sequência segura à variável chamada de ```$Passphrase```. O segundo comando define a cadeia de caracteres segura em ```$Passphrase``` como a senha para a criptografia de backups.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Mantenha as informações de senha seguras e protegidas depois de defini-las. Você não poderá restaurar os dados do Azure sem essa senha.
>
>

Neste ponto, você deve fez todas as alterações necessárias ao objeto ```$setting``` . Lembre-se de confirmar as alterações.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteger dados no Backup do Azure

Nesta seção, você adicionará um servidor de produção ao DPM, protegerá os dados primeiro no armazenamento local do DPM e, em seguida, no Backup do Azure. Nos exemplos, demonstraremos como fazer backup de arquivos e pastas. A lógica pode ser facilmente estendida para fazer backup de qualquer fonte de dados para a qual há suporte no DPM. Todos os backups do DPM são regidos por um GP (Grupo de Proteção) com quatro partes:

1. **Membros do grupo** é uma lista de todos os objetos protegíveis (também conhecidos como *Fontes de dados* no DPM) que você deseja proteger no mesmo grupo de proteção. Por exemplo, convém proteger as VMs de produção em um grupo de proteção e os bancos de dados do SQL Server em outro grupo de proteção, pois eles podem ter requisitos de backup diferentes. Antes de poder fazer backup de qualquer fonte de dados em um servidor de produção, é necessário verificar se o agente do DPM está instalado no servidor e é gerenciado pelo DPM. Siga as etapas para a [instalação do Agente do DPM](https://technet.microsoft.com/library/bb870935.aspx) e vincule-o ao Servidor DPM apropriado.
2. **Método de proteção de dados** especifica os locais de destino de backup – fita, disco e nuvem. Em nosso exemplo, protegeremos os dados no disco local e na nuvem.
3. Um **agendamento de backup** que especifica quando os backups precisam ser executados e a frequência com que os dados devem ser sincronizados entre o servidor DPM e o servidor de produção.
4. Um **cronograma de retenção** que especifica quanto tempo deve-se manter os pontos de recuperação no Azure.

### <a name="creating-a-protection-group"></a>Criando um grupo de proteção

Comece criando um novo Grupo de Proteção usando o cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima criará um Grupo de Proteção chamado *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado posteriormente para adicionar o backup à nuvem do Azure. No entanto, para fazer alterações ao Grupo de Proteção – novo ou existente – precisamos obter um identificador em um objeto *modificável* usando o cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Adicionando membros do grupo ao Grupo de Proteção

Cada Agente do DPM conhece a lista de fontes de dados no servidor em que está instalado. Para adicionar uma fonte de dados ao Grupo de Proteção, o Agente do DPM precisa enviar primeiro uma lista das fontes de dados de volta ao servidor DPM. Uma ou mais fontes de dados são então selecionadas e adicionadas ao Grupo de Proteção. As etapas do PowerShell necessárias para realizar isso são:

1. Obter uma lista de todos os servidores gerenciados pelo DPM por meio do Agente do DPM.
2. Escolher um servidor específico.
3. Obter uma lista de todas as fontes de dados no servidor.
4. Escolher uma ou mais fontes de dados e adicioná-las ao Grupo de Proteção

A lista dos servidores nos quais o Agente do DPM está instalado e sendo gerenciado pelo servidor DPM é obtida usando o cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) . Neste exemplo, filtraremos e configuraremos apenas o PS com o nome *productionserver01* para o backup.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains “productionserver01”}
```

Agora obtenha a lista de fontes de dados em ```$server``` usando o cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605). Neste exemplo, estamos filtrando para o volume *D:\\*  que desejamos configurar para backup. Esta fonte de dados é adicionada ao Grupo de Proteção usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732). Lembre-se de usar o objeto de grupo de proteção *modificável*```$MPG``` para fazer as adições.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains “D:\” }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita essa etapa quantas vezes for necessário, até adicionar todas as fontes de dados escolhidas ao grupo de proteção. Você também pode começar com apenas uma fonte de dados e concluir o fluxo de trabalho para criar o Grupo de Proteção e, posteriormente, adicionar mais fontes de dados ao Grupo de Proteção.

### <a name="selecting-the-data-protection-method"></a>Selecionando o método de proteção de dados

Depois de adicionar as fontes de dados ao Grupo de Proteção, a próxima etapa é especificar o método de proteção usando o cmdlet [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) . Neste exemplo, o Grupo de Proteção é configurado para backup no disco local e na nuvem. Você também precisa especificar a fonte de dados que deseja proteger na nuvem usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) com o sinalizador -Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Configurando o intervalo de retenção

Defina a retenção dos pontos de backup usando o cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) . Embora possa parecer estranho definir a retenção antes de definir o agendamento de backup, usar o cmdlet ```Set-DPMPolicyObjective``` define automaticamente um agendamento de backup padrão que pode então ser modificado. Sempre é possível definir o agendamento de backup primeiro e a política de retenção posteriormente.

No exemplo abaixo, o cmdlet define os parâmetros de retenção para backups em disco. Isso manterá backups por 10 dias e sincronizará dados entre o servidor de produção e o servidor DPM a cada 6 horas. O ```SynchronizationFrequencyMinutes``` não define a frequência em que um ponto de backup é criado, mas a frequência em que os dados são copiados para o servidor DPM.  Essa configuração impede que os backups fiquem grandes demais.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para backups destinados ao Azure (o DPM refere-se a eles como backups Online), os períodos de retenção podem ser configurados para a [retenção de longo prazo usando um esquema GFS (Avô-Pai-Filho)](backup-azure-backup-cloud-as-tape.md). Ou seja, você pode definir uma política de retenção combinada que envolva políticas de retenção diárias, semanais, mensais e anuais. Neste exemplo, criamos uma matriz que representa o esquema de retenção complexo que queremos e, em seguida, configuramos o intervalo de retenção usando o cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Definir o agendamento de backup

O DPM define um agendamento padrão automaticamente se você especificar o objetivo de proteção usando o cmdlet ```Set-DPMPolicyObjective``` . Para alterar os agendamentos padrão, use o cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido do cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723).

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` é uma matriz com quatro elementos que contém o agendamento de proteção online existente para o Grupo de Proteção no esquema GFS:

1. ```$onlineSch[0]``` contém a agenda diária
2. ```$onlineSch[1]``` contém a agenda semanal
3. ```$onlineSch[2]``` contém a agenda mensal
4. ```$onlineSch[3]``` contém a agenda anual

Portanto, se precisar modificar o agendamento semanal, você precisa se referir a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Backup inicial

Ao fazer backup de uma fonte de dados pela primeira vez, o DPM precisa criar uma réplica inicial que cria uma cópia completa da fonte de dados que será protegida no volume de réplica do DPM. Essa atividade pode ser agendada para um horário específico ou pode ser disparada manualmente usando o cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) com o parâmetro ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Alterando o tamanho da réplica do DPM e o volume de ponto de recuperação

Você também pode alterar o tamanho do volume da réplica do DPM e o volume da cópia de sombra usando o cmdlet [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) como no exemplo a seguir: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Confirmando as alterações ao Grupo de Proteção

Por último, as alterações precisam ser confirmadas antes que o DPM possa fazer o backup de acordo com a nova configuração do Grupo de Proteção. Isso pode ser obtido usando o cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Exibir os pontos de backup

Você pode usar o cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) para obter uma lista de todos os pontos de recuperação para uma fonte de dados. Neste exemplo, iremos:

* buscar todos os PGs no servidor DPM e armazenados em uma matriz ```$PG```
* obter as fontes de dados correspondente ao ```$PG[0]```
* obter todos os pontos de recuperação para uma fonte de dados.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar dados protegidos no Azure

A restauração de dados é uma combinação de um objeto ```RecoverableItem``` e um objeto ```RecoveryOption```. Na seção anterior, obtivemos uma lista de pontos de backup para uma fonte de dados.

No exemplo a seguir, demonstramos como restaurar uma máquina virtual Hyper-V por meio do Backup do Azure, combinando os pontos de backup com o destino para recuperação. Este exemplo inclui:

* Criar uma opção de recuperação usando o cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) .
* Obter a matriz de pontos de backup usando o cmdlet ```Get-DPMRecoveryPoint``` .
* Escolher um ponto de backup por meio do qual fazer a restauração.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de fonte de dados.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o DPM para o Backup do Azure, confira [Introdução ao Backup do DPM](backup-azure-dpm-introduction.md)
