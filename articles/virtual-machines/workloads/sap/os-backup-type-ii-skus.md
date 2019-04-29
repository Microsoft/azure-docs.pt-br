---
title: Backup do sistema operacional e restauração do SAP HANA em SKUs do tipo II (Instâncias Grandes) do Azure | Microsoft Docs
description: Realizar backup do sistema operacional e restauração do SAP HANA em SKUs do tipo II (Instâncias Grandes) do Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708545"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Backup e restauração do sistema operacional para SKUs do tipo II

Este documento descreve as etapas para executar um backup e uma restauração em nível de arquivo do sistema operacional para as **SKUs Tipo II** das instâncias grandes do HANA. 

>[!NOTE]
>Os scripts de backup do sistema operacional usam o software ReaR, que vem pré-instalado no servidor.  

Depois que o provisionamento for concluído pela equipe do Microsoft Service Management, por padrão, o servidor será configurado com dois agendamentos de backups para fazer backup do backup do sistema operacional no nível do sistema de arquivos. Verifique o status do agendamento do trabalho de backup usando o comando a seguir:
```
#crontab –l
```
É possível alterar o agendamento de backup a qualquer momento usando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como fazer um backup manual?

O backup do sistema operacional já é agendado usando um **trabalho cron**. No entanto, você também pode executar manualmente o backup em nível de arquivo do sistema operacional. Para fazer um backup manual, execute o seguinte comando:

```
#rear -v mkbackup
```
A tela a seguir mostra o backup manual do exemplo:

![como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar um backup?

É possível restaurar um backup completo ou um arquivo individual do backup. Para restaurar, use o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após a restauração, o arquivo é recuperado no diretório de trabalho atual.

O comando a seguir mostra a restauração de um arquivo */etc/fstabfrom* do arquivo de backup *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>É necessário copiar o arquivo para o local desejado após a restauração do backup.

Captura de tela a seguir mostra a restauração de um backup completo:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta ReaR e alterar a configuração? 

Os pacotes ReaR (Relax-and-Recover) vêm **pré-instalados** em **SKUs do tipo II** de HANA de Grandes Instâncias e nenhuma ação é necessária. É possível iniciar diretamente usando o ReaR para o backup do sistema operacional.
No entanto, caso você precise instalar os pacotes no seu próprio, siga as etapas listadas para instalar e configurar a ferramenta ReaR.

Para instalar os pacotes de backup do **ReaR**, use os seguintes comandos:

Para sistemas operacionais **SLES**, use o seguinte comando:
```
#zypper install <rear rpm package>
```
Para sistemas operacionais **RHEL**, use o seguinte comando: 
```
#yum install rear -y
```
Para configurar a ferramenta ReaR, você precisa atualizar os parâmetros **OUTPUT_URL** e **BACKUP_URL** no *arquivo /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Captura de tela a seguir mostra a restauração de um backup completo: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
