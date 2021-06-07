---
title: Instalação silenciosa do Servidor de Backup do Azure V2
description: Use um script do PowerShell para instalar silenciosamente o Servidor de Backup do Azure V2. Esse tipo de instalação também é chamado de uma instalação autônoma.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "74172236"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Executar uma instalação autônoma do Servidor de Backup do Azure

Saiba como executar uma instalação autônoma do Servidor de Backup do Azure.

Essas etapas não se aplicam se você estiver instalando o Servidor de Backup do Azure v1.

## <a name="install-backup-server"></a>Instalar o servidor de backup

1. No servidor que hospeda o Servidor de Backup do Azure V2 ou posterior, crie um arquivo de texto. (Você pode criar o arquivo no bloco de notas ou em outro editor de texto.) Salve o arquivo como MABSSetup.ini.

2. Cole o código a seguir no arquivo MABSSetup.ini. Substitua o texto dentro dos colchetes (\< \>) com valores do seu ambiente. O texto a seguir é um exemplo:

   ```text
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

   ```cmd
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
