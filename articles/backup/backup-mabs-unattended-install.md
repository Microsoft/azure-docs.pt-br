---
title: Instalação silenciosa do Servidor de Backup do Azure V2
description: Use um script do PowerShell para instalar silenciosamente o Servidor de Backup do Azure V2. Esse tipo de instalação também é chamado de uma instalação autônoma.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: add1f4057b5b52310f53553dcd23e3357fb1ee29
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465019"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Executar uma instalação autônoma do Servidor de Backup do Azure

Saiba como executar uma instalação autônoma do Servidor de Backup do Azure.

Essas etapas não serão aplicáveis se você estiver instalando o Servidor de Backup do Azure V1.

## <a name="install-backup-server"></a>Instalar o servidor de backup

1. No servidor que hospeda o Servidor de Backup do Azure V2 ou posterior, crie um arquivo de texto. (Você pode criar o arquivo no Bloco de Notas ou em outro editor de texto.) Salve o arquivo como MABSSetup.ini.

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
**/l**: Caminho do log</br>
**/i**: Caminho de instalação</br>
**/x**: Caminho de desinstalação</br>

## <a name="next-steps"></a>Próximas etapas
Depois de instalar o Servidor de Backup, saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Adicionar Armazenamento de Backup Moderno para o Servidor de Backup](backup-mabs-add-storage.md)
