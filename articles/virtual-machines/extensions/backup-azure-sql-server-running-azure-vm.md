---
title: Backup do Azure para SQL Server em execução na VM do Azure
description: Como registrar o backup do Azure SQL Server em execução na VM do Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871892"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup do Azure para SQL Server em execução na VM do Azure

O backup do Azure, entre outras ofertas, oferece suporte ao backup de cargas de trabalho, como SQL Server em execução em VMs do Azure. Como o aplicativo SQL está sendo executado em uma VM do Azure, o serviço de backup precisa de permissão para acessar o aplicativo e buscar os detalhes necessários.
Para fazer isso, o backup do Azure instala a extensão **AzureBackupWindowsWorkload** na VM, na qual o SQL Server está em execução, durante o processo de registro disparado pelo usuário.

## <a name="prerequisites"></a>Pré-requisitos

Para obter a lista de cenários com suporte, consulte a [matriz de suporte](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) com suporte do backup do Azure.

## <a name="network-connectivity"></a>Conectividade de rede

O backup do Azure dá suporte a marcas NSG, implantando um servidor proxy ou intervalos de IP listados; para obter detalhes sobre cada um dos métodos, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Esquema de extensão

O esquema de extensão e os valores de propriedade são os valores de configuração (configurações de tempo de execução) que o serviço está passando para a API do CRP. Esses valores de configuração são usados durante o registro e a atualização. A extensão **AzureBackupWindowsWorkload** também usa esse esquema. O esquema é predefinido; um novo parâmetro pode ser adicionado no campo objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

O JSON a seguir mostra o esquema para a extensão WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Valores de propriedade

Nome | Valor/exemplo | Tipo de dados
 --- | --- | ---
localidade | pt-BR  |  cadeia de caracteres
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | cadeia de caracteres
objectStr <br/> PublicSettings  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | cadeia de caracteres
commandStartTimeUTCTicks | "636967192566036845"  | cadeia de caracteres
vmType  | "microsoft.compute/virtualmachines"  | cadeia de caracteres
objectStr <br/> ProtectedSettings | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | cadeia de caracteres
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | cadeia de caracteres
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | cadeia de caracteres


## <a name="template-deployment"></a>Implantação de modelo

Recomendamos adicionar a extensão AzureBackupWindowsWorkload a uma máquina virtual, habilitando SQL Server backup na máquina virtual. Isso pode ser obtido por meio do [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) projetado para automatizar o backup em uma VM SQL Server.


## <a name="powershell-deployment"></a>Implantação do PowerShell

Você precisa ' Registrar ' a VM do Azure que contém o aplicativo SQL com um cofre dos serviços de recuperação. Durante o registro, a extensão AzureBackupWindowsWorkload é instalada na VM. Use o cmdlet [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) para registrar a VM.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
O comando retornará um **contêiner de backup** desse recurso e o status será **registrado**.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) sobre as diretrizes de solução de problemas de backup de VM do Azure SQL Server
- [Perguntas comuns](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sobre como fazer backup de bancos de dados SQL Server executados em VMS (máquinas virtuais) do Azure e que usam o serviço de backup do Azure.
