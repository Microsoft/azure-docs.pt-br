---
title: "Instalação silenciosa do Servidor de Backup do Azure v2 | Microsoft Docs"
description: "Use um script do PowerShell para instalar silenciosamente o Servidor de Backup do Azure v2. Esse tipo de instalação também é chamado de uma instalação autônoma."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Executar uma instalação autônoma do Servidor de Backup do Azure v2

Saiba como executar uma instalação autônoma do Servidor de Backup do Azure v2. 

Essas etapas não se aplicam se você estiver instalando o Servidor de Backup do Azure v1.

## <a name="install-backup-server-v2"></a>Instalar o Servidor de Backup v2

1. No servidor que hospeda o Servidor de Backup do Azure v2, crie um arquivo de texto. (Você pode criar o arquivo no Bloco de Notas ou em outro editor de texto.) Salve o arquivo como MABSSetup.ini. 

2. Cole o código a seguir no arquivo MABSSetup.ini. Substitua o texto dentro dos colchetes (\< \>) com valores do seu ambiente. O texto a seguir é um exemplo:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Salve o arquivo. Em seguida, em um prompt de comando com privilégios elevados no servidor de instalação, digite este comando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Você pode usar esses sinalizadores para a instalação:</br>
**/f**: .ini caminho do arquivo</br>
**/l**: caminho do log</br>
**/i**: caminho de instalação</br>
**/x**: desinstalar o caminho</br>

## <a name="next-steps"></a>Próximas etapas
Depois de instalar o Servidor de Backup, saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Adicionar Armazenamento de Backup Moderno para o Servidor de Backup](backup-mabs-add-storage.md)
