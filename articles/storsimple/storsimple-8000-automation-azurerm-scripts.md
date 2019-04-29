---
title: Usar scripts do Azure Resource Manager para gerenciar dispositivos StorSimple | Microsoft Docs
description: Saiba como usar scripts do Azure Resource Manager para automatizar trabalhos do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 646b862733e8727c9c8729f1ac038fa88cfa0580
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726410"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Usar scripts com base no SDK do Azure Resource Manager para gerenciar dispositivos StorSimple

Este artigo descreve como os scripts baseados no SDK do Azure Resource Manager podem ser usados para gerenciar seu dispositivo StorSimple da série 8000. Também há um exemplo de script para orientar você pelas etapas de configuração do ambiente para execução desses scripts.

Este artigo se aplica aos dispositivos StorSimple da série 8000 em execução apenas no Portal do Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

Os scripts de exemplo a seguir estão disponíveis para automatizar vários trabalhos do StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela de scripts de exemplo com base no SDK do Azure Resource Manager

| Script do Azure Resource Manager                    | DESCRIÇÃO                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Esse script permite que você autorize seu dispositivo StorSimple para alterar a chave de criptografia de dados do serviço.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Esse script cria um Dispositivo de Nuvem StorSimple 8010 ou 8020. O dispositivo de nuvem pode ser configurado e registrado com o serviço StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Esse script cria ou modifica volumes do StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Esse script lista todos os backups de um dispositivo registrado com o serviço Gerenciador de Dispositivos StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Isso gera um script de todas as políticas de backup de seu dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Este script obtém todos os trabalhos do StorSimple em execução em seu serviço Gerenciador de Dispositivos StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Esse script examina o servidor de Atualização e avisa se houver atualizações disponíveis para instalação em seu dispositivo StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Esse script instala as atualizações disponíveis em seu dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Esse script inicia um instantâneo de nuvem manual e exclui os instantâneos de nuvem que ultrapassaram os dias de retenção especificados.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script do PowerShell do Runbook de Automação do Azure informa o status de todos os trabalhos de backup.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Esse script exclui um único objeto de backup.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Esse script inicia um backup manual em seu dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Esse script atualiza a chave de criptografia de dados de serviço para todos os Dispositivos de Nuvem StorSimple 8010/8020 registrados com o serviço Gerenciador de Dispositivos StorSimple.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Esse script realça os backups ausentes depois de analisar todos os agendamentos associados às políticas de backup. Ela também verifica o catálogo de backups com a lista de backups disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configurar e executar um exemplo de script

Esta seção usa um script de exemplo e fornece detalhes sobre as várias etapas necessárias para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

*   Azure Powershell instalado. Para instalar os módulos do Azure PowerShell:
    * Em um ambiente do Windows, execute as etapas em [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Você pode instalar o Azure PowerShell em seu host do Windows Server para o StorSimple, se estiver usando um.
    * Em um ambiente do Linux ou MacOS, execute as etapas em [Instalar e configurar o Azure PowerShell no MacOS ou Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Para saber mais sobre como usar o Azure PowerShell, acesse [Introdução ao uso do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Executar o script do Azure PowerShell

O script usado neste exemplo lista todos os trabalhos em um dispositivo StorSimple. Isso inclui os trabalhos que tiveram êxito, falharam ou estão em andamento. Execute as etapas a seguir para baixar e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Baixe a CLI do NuGet](https://www.nuget.org/downloads) na pasta criada na etapa anterior. Há várias versões do _nuget.exe_. Escolha a versão correspondente ao seu SDK. Cada link de download aponta diretamente para um arquivo _.exe_. Clique com o botão direito e salve o arquivo em seu computador, em vez de executá-lo no navegador.

    Você também pode executar o comando a seguir para baixar e armazenar o script na mesma pasta que você criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Baixe o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Baixe o script do projeto de exemplo do GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Execute o script. Quando a autenticação for solicitada, forneça suas credenciais do Azure. Esse script deve gerar uma lista filtrada com todos os trabalhos em seu dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Saída de exemplo

A saída a seguir será apresentada quando o script de exemplo for executado. A saída contém todos os trabalhos executados em um dispositivo registrado iniciados em 25 de setembro de 2017 e concluídas em 2 de outubro de 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Próximas etapas

[Usar o serviço do Gerenciador de Dispositivos StorSimple para gerenciar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).