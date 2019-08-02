---
title: Backup do sistema operacional e restauração do SAP HANA em SKUs do tipo II (Instâncias Grandes) do Azure | Microsoft Docs
description: Executar backup e restauração do sistema operacional para SAP HANA em SKUs do tipo II do Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3afcd429351a0d988ff0e82ecf09f524ceac70f1
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868961"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Backup e restauração do so para SKUs do tipo II de carimbos de revisão 3

Este documento descreve as etapas para executar um backup e restauração no nível de arquivo do sistema operacional para os **SKUs do tipo II** das instâncias grandes Hana da revisão 3. 

>[!Important]
> **Este artigo não se aplica a implantações de SKU do tipo II na revisão 4 carimbos de instância grande do HANA.** LUNS de inicialização do tipo II as unidades de instância grande do HANA que são implantadas na revisão 4 é possível fazer backup de carimbos de instância grande do HANA com instantâneos de armazenamento, pois esse é o caso com SKUs do tipo I já nos carimbos de revisão 3


>[!NOTE]
>Os scripts de backup do sistema operacional usam o software ReaR, que vem pré-instalado no servidor.  

Depois que o provisionamento for concluído pela equipe da Microsoft `Service Management` , por padrão, o servidor será configurado com duas agendas de backup para fazer backup do nível do sistema de arquivos de volta do sistema operacional. Você pode verificar os agendamentos dos trabalhos de backup usando o seguinte comando:
```
#crontab –l
```
Você pode alterar o agendamento de backup a qualquer momento usando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como fazer um backup manual?

O backup do sistema de arquivos do so já está agendado usando um **trabalho cron** . No entanto, você também pode executar manualmente o backup em nível de arquivo do sistema operacional. Para fazer um backup manual, execute o seguinte comando:

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

A captura de tela a seguir mostra a restauração de um backup completo:

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

A captura de tela a seguir mostra a restauração de um backup completo: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
