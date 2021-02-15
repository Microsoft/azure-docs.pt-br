---
title: Usar o PowerShell para fazer backup do Windows Server no Azure
description: Neste artigo, saiba como usar o PowerShell para configurar o backup do Azure no Windows Server ou um cliente do Windows e gerenciar o backup e a recuperação.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987099"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implantar e gerenciar o backup no Azure para o Windows Server/Windows Client usando o PowerShell

Este artigo mostra como usar o PowerShell para configurar o backup do Azure no Windows Server ou um cliente do Windows e gerenciar o backup e a recuperação.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [instale a versão mais recente do PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de Backup.

1. Se você estiver usando o backup do Azure pela primeira vez, deverá usar o cmdlet **Register-AzResourceProvider** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos serviços de recuperação é um recurso Azure Resource Manager, portanto, você precisa colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e o local para o grupo de recursos.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Use o cmdlet **New-AzRecoveryServicesVault** para criar um novo cofre. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Especifique o tipo de redundância de armazenamento a usar. Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy.md#locally-redundant-storage), o [grs (armazenamento com redundância geográfica)](../storage/common/storage-redundancy.md#geo-redundant-storage) ou o [ZRS (armazenamento com redundância de zona)](../storage/common/storage-redundancy.md#zone-redundant-storage). O exemplo a seguir mostra a opção **-BackupStorageRedundancy** para *testVault* definida como **georedundância**.

   > [!TIP]
   > Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, é conveniente armazenar o objeto de backup do cofre dos Serviços de Recuperação em uma variável.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Use **Get-AzRecoveryServicesVault** para ver a lista de todos os cofres da assinatura atual. Você pode usar esse comando para verificar se um novo cofre foi criado ou para ver quais cofres estão disponíveis na assinatura.

Execute o comando **Get-AzRecoveryServicesVault** e todos os cofres na assinatura serão listados.

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

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalando o agente de Backup do Azure

Antes de instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a última versão do instalador no [Centro de Download da Microsoft](https://aka.ms/azurebackup_agent) ou na página Painel do cofre dos Serviços de Recuperação. Salve o instalador em uma localização de fácil acesso, como `C:\Downloads\*`.

Como alternativa, use o PowerShell para obter o downloader:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Para instalar o agente, execute o comando a seguir em um console do Azure PowerShell com privilégios elevados:

```powershell
MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção */nu* , a janela de **Windows Update** será aberta no final da instalação para verificar se há atualizações. Uma vez instalado, o agente será exibido na lista de programas instalados.

Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrando o Windows Server ou o computador cliente Windows em um cofre dos serviços de recuperação

Depois de criar o cofre dos Serviços de Recuperação, baixe o agente mais recente e as credenciais do cofre e armazene-os em um local conveniente como C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registro usando o módulo PS Az

> [!NOTE]
> Um bug na geração de certificado de cofre foi corrigido na versão Az 3.5.0. Use a versão de lançamento do Az 3.5.0 ou superior para baixar um certificado de cofre.

No módulo AZ mais recente do PowerShell, devido a limitações da plataforma subjacente, o download das credenciais do cofre requer um certificado autoassinado. O exemplo a seguir mostra como fornecer um certificado autoassinado e baixar as credenciais do cofre.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

No computador cliente do Windows Server ou do Windows, execute o cmdlet [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) para registrar o computador no cofre.
Isso, e outros cmdlets usados para backup, são do módulo MSONLINE, que o MARS AgentInstaller adicionou como parte do processo de instalação.

O instalador do agente não atualiza o $Env:P variável SModulePath. Isso significa que o carregamento automático do módulo falhará. Para resolver esse problema, você pode fazer o seguinte:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Como alternativa, você pode carregar manualmente o módulo em seu script da seguinte maneira:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Depois de carregar os cmdlets do Backup Online, você registra as credenciais do cofre:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Configurações de rede

Quando a conectividade do computador Windows com a internet for através de um servidor proxy, as configurações de proxy também podem ser fornecidas para o agente. Neste exemplo, não há nenhum servidor proxy, portanto, estamos limpando explicitamente qualquer informação relacionada ao proxy.

O uso de largura de banda também pode ser controlado com as opções de `work hour bandwidth` e `non-work hour bandwidth` para um determinado conjunto de dias da semana.

A configuração dos detalhes de proxy e largura de banda é feita usando o [Set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) cmdlet:

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Configurações de criptografia

Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração.

Gere um PIN de segurança selecionando **Gerar**, em **Configurações** > **Propriedades** > **PIN de segurança** na seção **Cofre dos Serviços de Recuperação** do portal do Azure.

>[!NOTE]
> O PIN de segurança só pode ser gerado através do portal do Azure.

Depois, use-o como o `generatedPIN` no comando:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Mantenha as informações de senha seguras e seguras após sua definição. Você não pode restaurar os dados do Azure sem essa frase secreta.
>
>

## <a name="back-up-files-and-folders"></a>Fazer backup de arquivos e pastas

Todos os backups de Windows Servers e de clientes para o Backup do Azure são controlados por uma política. A política inclui três partes:

1. Um **agendamento de backup** que especifica quando backups precisam ser efetuados e sincronizados com o serviço.
2. Um **cronograma de retenção** que especifica quanto tempo deve-se manter os pontos de recuperação no Azure.
3. Uma **especificação de inclusão/exclusão de arquivo** que determina de que conteúdo deve-se realizar o backup.

Neste documento, já que estamos automatizando o backup, vamos pressupor que nada foi configurado. Começamos pela criação de uma nova política de backup usando o cmdlet [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) .

```powershell
$NewPolicy = New-OBPolicy
```

Neste momento, a política está vazia e outros cmdlets são necessários para definir quais itens serão incluídos ou excluídos, quando os backups serão executados e onde os backups serão armazenados.

### <a name="configuring-the-backup-schedule"></a>Configurando o agendamento de backup

A primeira das três partes de uma política é o agendamento de backup, que é criado usando o cmdlet [New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule). O agendamento de backup define quando os backups precisam ser executados. Quando criar um agendamento, você precisa especificar dois parâmetros de entrada:

* **dias da semana** nos quais o backup deve ser executado. Você pode executar o trabalho de backup em apenas um dia ou em todos os dias da semana, ou qualquer combinação entre essas opções.
* **horários do dia** quando o backup deve ser executado. Você pode definir até três horários do dia diferentes para disparar o backup.

Por exemplo, você poderia configurar uma política de backup que é executada às 16h todo sábado e domingo.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

O agendamento de backup deve ser associado a uma política, e isso pode ser realizado usando o cmdlet [Set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Configurando uma política de retenção

A política de retenção define por quanto tempo os pontos de recuperação criados por meio de trabalhos de backup são mantidos. Ao criar uma nova política de retenção usando o cmdlet [New-OBRetentionPolicy](/powershell/module/msonlinebackup/new-obretentionpolicy) , você pode especificar o número de dias que os pontos de recuperação de backup serão retidos com o backup do Azure. O exemplo a seguir define uma política de retenção de sete dias.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção deve ser associada à política principal usando o cmdlet [Set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Incluindo e excluindo arquivos destinados a backup

Um objeto `OBFileSpec` define os arquivos a serem incluídos e excluídos em um backup. Este é um conjunto de regras que definem o escopo dos arquivos e pastas protegidos em um computador. Você pode ter muitas regras de inclusão ou exclusão de arquivos conforme necessário e associá-las a uma política. Ao criar um novo objeto OBFileSpec, você pode:

* Especificar os arquivos e pastas a serem incluídos
* Especificar os arquivos e pastas a serem excluídos
* Especificar o backup recursivo de dados em uma pasta ou o backup apenas dos arquivos de nível superior na pasta especificada.

A última opção é obtida usando o sinalizador -NonRecursive no comando New-OBFileSpec.

No exemplo a seguir, vamos fazer backup dos volumes C: e D: e excluir os binários do sistema operacional na pasta do Windows e em quaisquer pastas temporárias. Para isso, vamos criar duas especificações de arquivo usando o cmdlet [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) – uma para inclusão e outra para exclusão. Depois que as especificações de arquivo são criadas, elas são associadas à política usando o cmdlet [Add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Aplicando a política

Agora, o objeto de política está concluído e tem um agendamento de backup associado, política de retenção e uma lista de inclusão/exclusões de arquivos. Esta política agora pode ser confirmada para uso pelo Backup do Azure. Antes de aplicar a política recém-criada, verifique se não há nenhuma política de backup existente associada ao servidor, por meio do cmdlet [Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) . Remover a política gerará uma solicitação de confirmação. Para ignorar a confirmação, use o sinalizador `-Confirm:$false` com o cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A confirmação do objeto de política é realizada usando o cmdlet [Set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) . Isso também gerará uma solicitação de confirmação. Para ignorar a confirmação, use o sinalizador `-Confirm:$false` com o cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Você pode exibir os detalhes da política de backup existente usando o cmdlet [Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) . Você pode fazer drill down ainda mais usando o cmdlet [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) para o agendamento de backup e o cmdlet [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) para as políticas de retenção

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Realização de um backup sob demanda

Depois de definir uma política de backup, os backups ocorrerão de acordo com o agendamento. Disparar um backup sob demanda também é possível usando o cmdlet [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup):

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>Fazer backup do estado do sistema do Windows Server no agente MARS

Esta seção aborda o comando do PowerShell para configurar o estado do sistema no agente MARS

### <a name="schedule"></a>Agenda

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Retenção

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Configuração de agendamento e retenção

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verificação da política

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Restaurar dados por meio do Backup do Azure

Esta seção o orientará pelas etapas para automatizar a recuperação de dados por meio do Backup do Azure. Isso envolve as seguintes etapas:

1. Selecionar o volume de origem
2. Escolher um ponto de backup para restaurar
3. Especifique um item para restaurar
4. Disparar o processo de restauração

### <a name="picking-the-source-volume"></a>Selecionar o volume de fonte

Para restaurar um item do backup do Azure, primeiro você precisa identificar a origem do item. Como estamos executando os comandos no contexto de um Windows Server ou um cliente Windows, o computador já foi identificado. A próxima etapa para identificação da origem é identificar o volume que a contém. É possível recuperar uma lista de volumes ou fontes cujo backup está sendo executado neste computador pela execução do cmdlet [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) . Esse comando retorna uma matriz de todas as fontes desse servidor/cliente cujo backup foi executado.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Escolhendo um ponto de backup do qual restaurar

Você recupera uma lista de pontos de backup ao executar o cmdlet [Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) com os parâmetros apropriados. Em nosso exemplo, escolheremos o último ponto de backup para o volume de origem *C:* e o usaremos para recuperar um arquivo específico.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

O objeto `$Rps` é uma matriz de pontos de backup. O primeiro elemento é o ponto mais recente e o enésimo elemento é o ponto mais antigo. Para escolher o ponto mais recente, usaremos `$Rps[0]` .

### <a name="specifying-an-item-to-restore"></a>Especificação de um item para restauração

Para restaurar um arquivo específico, especifique o nome do arquivo relativo ao volume raiz. Por exemplo, para recuperar C:\Test\Cat.job, execute o comando a seguir.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Disparando o processo de restauração

Para disparar o processo de restauração, primeiro precisamos especificar as opções de recuperação. Isso pode ser feito usando o cmdlet [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) . Para este exemplo, vamos supor que desejamos restaurar os arquivos para *C:\temp*. Vamos supor também que queremos ignorar os arquivos que já existem na pasta de destino *C:\temp*. Para criar essa opção de recuperação, use o seguinte comando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora dispare o processo de restauração usando o comando [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) no `$Item` selecionado na saída do cmdlet `Get-OBRecoverableItem`:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalando o agente de Backup do Azure

A desinstalação do agente de Backup do Azure pode ser feita usando o seguinte comando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Antes de desinstalar os binários do agente do computador, é necessário considerar algumas consequências:

* A desinstalação remove o filtro de arquivo do computador e interrompe o acompanhamento de alterações.
* Todas as informações de política são removidas do computador, mas as informações de política continuam sendo armazenadas no serviço.
* Todos os agendamentos de backup são removidos e nenhum backup posterior é realizado.

No entanto, os dados armazenados no Azure permanecem e são mantidos de acordo com a configuração da política de retenção por você. Os pontos mais antigos são desatualizados automaticamente.

## <a name="remote-management"></a>Gerenciamento remoto

Todo o gerenciamento relacionado ao agente, às políticas e às fontes de dados do Backup do Azure pode ser feito remotamente por meio do PowerShell. A máquina que será gerenciada remotamente precisa ser preparada corretamente.

Por padrão, o serviço WinRM é configurado para inicialização manual. O tipo de inicialização deve ser definido como *Automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade Status deve ser *Em execução*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

O PowerShell deve ser configurado remotamente.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

O computador agora pode ser gerenciado remotamente - começando na instalação do agente. Por exemplo, o script a seguir copia o agente para o computador remoto e o instala.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Backup do Azure para Windows Server/Client:

* [Introdução ao Backup do Azure](./backup-overview.md)
* [Fazer backup de servidores Windows](backup-windows-with-mars-agent.md)
