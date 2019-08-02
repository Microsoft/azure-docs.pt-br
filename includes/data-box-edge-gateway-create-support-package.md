---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172381"
---
Se você enfrentar problemas no dispositivo, você pode criar um pacote de suporte dos logs do sistema. Microsoft Support usará esse pacote para solucionar os problemas. Siga estas etapas para criar um pacote de suporte:

1. [Conectar-se à interface do PowerShell do seu dispositivo](#connect-to-the-powershell-interface).
2. Use o `Get-HcsNodeSupportPackage` comando para criar um pacote de suporte. O uso do cmdlet é da seguinte maneira:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    O cmdlet coleta logs do seu dispositivo e copia esses logs para uma rede especificado ou compartilhamento local.

    Os parâmetros usados são da seguinte maneira:

    - `-Path` -Especifique a rede ou o caminho local para copiar o pacote de suporte. (obrigatório)
    - `-Credential` -Especifique as credenciais para acessar o caminho protegido.
    - `-Zip` -Especifique para gerar um arquivo zip.
    - `-Include` -Especifique para incluir os componentes a serem incluídos no pacote de suporte. Se não for especificado, `Default` será assumido.
    - `-IncludeArchived` -Especifique para incluir os logs arquivados no pacote de suporte.
    - `-IncludePeriodicStats` -Especifique para incluir logs stat periódicos no pacote de suporte.

    
