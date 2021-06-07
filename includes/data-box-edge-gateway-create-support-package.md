---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "67172381"
---
Se você tiver problemas de dispositivo, poderá criar um pacote de suporte nos logs do sistema. Suporte da Microsoft usa esse pacote para solucionar os problemas. Siga estas etapas para criar um pacote de suporte:

1. [Conecte-se à interface do PowerShell do seu dispositivo](#connect-to-the-powershell-interface).
2. Use o `Get-HcsNodeSupportPackage` comando para criar um pacote de suporte. O uso do cmdlet é o seguinte:

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

    O cmdlet coleta logs do seu dispositivo e copia esses logs para uma rede ou um compartilhamento local especificado.

    Os parâmetros usados são os seguintes:

    - `-Path` -Especifique a rede ou o caminho local para o qual copiar o pacote de suporte. (obrigatório)
    - `-Credential` -Especifique as credenciais para acessar o caminho protegido.
    - `-Zip` -Especifique para gerar um arquivo zip.
    - `-Include` -Especifique para incluir os componentes a serem incluídos no pacote de suporte. Se não for especificado, `Default` será assumido.
    - `-IncludeArchived` -Especifique para incluir logs arquivados no pacote de suporte.
    - `-IncludePeriodicStats` -Especifique para incluir logs de estatísticas periódicas no pacote de suporte.

    
