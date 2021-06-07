---
title: Restaurar usando o Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para executar o comando Restore do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865257"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Restaurar usando a ferramenta de instantâneo consistente do Aplicativo Azure (versão prévia)

Este artigo fornece um guia para executar o comando Restore do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

Fazer uma restauração de volume de um instantâneo é feito usando o `azacsnap -c restore` comando.

> [!IMPORTANT]
> Isso não executa uma recuperação de banco de dados, somente uma restauração de volume, conforme descrito para cada uma das opções abaixo.

## <a name="command-options"></a>Opções de comando

O `-c restore` comando tem as seguintes opções:

- `--restore snaptovol` Cria um novo volume com base no instantâneo mais recente no volume de destino. Esse comando cria um novo volume "clonado" com base no volume de destino configurado, usando o instantâneo de volume mais recente como base para criar o novo volume.  Esse comando não interrompe a replicação de armazenamento do primário para o secundário. Em vez disso, os clones do instantâneo mais recente disponível são criados no local de recuperação de desastre e os montagem de sistema de arquivos recomendados dos volumes clonados são apresentados. Esse comando deve ser executado no sistema de instância grande do Azure **na região de Dr** (ou seja, o sistema de failover de destino).

- `--restore revertvolume` Reverte o volume de destino para um estado anterior com base no instantâneo mais recente.  Usando esse comando como parte do failover de DR na região de DR emparelhada. Esse comando **interrompe** a replicação de armazenamento do site primário para o site secundário e reverte os volumes de Dr de destino para seus instantâneos mais recentes disponíveis nos volumes de recuperação de desastres, juntamente com os montagem de sistema de arquivos recomendados para os volumes de Dr revertidos. Esse comando deve ser executado no sistema de instância grande do Azure **na região de Dr** (ou seja, o sistema de failover de destino).
    > [!NOTE]
    > O subcomando ( `--restore revertvolume` ) só está disponível para a instância grande do Azure e não está disponível para Azure NetApp files.
- `--dbsid <SAP HANA SID>` é o SAP HANA SID que está sendo selecionado no arquivo de configuração para aplicar os comandos de restauração de volume ao.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de arquivo de configuração personalizada.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Executar um failover de teste de recuperação de desastre `azacsnap -c restore --restore snaptovol`

Esse comando é como o comando de failover de DR "completo" ( `--restore restorevolume` ), mas em vez de interromper a replicação entre o site primário e o site de recuperação de desastres, um volume de clone é criado fora dos volumes de recuperação de desastres, permitindo a restauração do instantâneo mais recente no site de recuperação de desastres. Esses volumes clonados são utilizáveis pelo cliente para testar a recuperação de desastres sem a necessidade de executar um failover completo de seu ambiente do HANA que interrompe o contrato de replicação entre o site primário e o site de recuperação de desastre.

- Vários pontos de restauração diferentes podem ser testados dessa forma, cada um com seu próprio ponto de restauração.
- O clone é designado pelo carimbo de data/hora em que o comando foi executado e representa os dados mais recentes e outros instantâneos disponíveis quando executados.

> [!IMPORTANT]
> Esta operação se aplica somente à instância grande do Azure.
>
> - Quando esse comando é executado, ele requer que o email de contato para operações Liaise com o antes da exclusão dos clones após 4 semanas.
> - Cada execução desse comando criará um novo clone que deve ser excluído por operações da Microsoft quando o teste for concluído.
> - Todos os volumes de clone criados serão excluídos automaticamente após 4 semanas.

O arquivo de configuração (por exemplo, `DR.json` ) deve conter apenas os volumes de recuperação de desastre e não os volumes de produção, caso contrário, os volumes de produção podem ter clones criados.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Saída do `azacsnap -c restore --restore snaptovol` comando (para o cenário de Single-Node)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> A saída "exibindo pontos de montagem por volume" é diferente para os vários cenários.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Executar failover de DR completo `azacsnap -c restore --restore revertvolume`

Esse comando **interrompe** a replicação de armazenamento do site primário para o site secundário, restaura o instantâneo mais recente nos volumes de recuperação de desastres e fornece o montagem para os volumes de recuperação de desastres.

Esse comando deve ser executado no servidor de recuperação de desastre usando um arquivo de configuração (por exemplo, `DR.json` ) somente com volumes de recuperação de desastres!

Execute um failover para um site de DR, executando o comando `azacsnap -c restore --restore revertvolume` .  Este comando requer que um SID seja adicionado como um parâmetro. Esse é o SID da instância do HANA, que precisa ser recuperado no local de recuperação de desastre.

> [!IMPORTANT]
> Somente execute este comando se você estiver planejando executar o exercício de DR ou um teste. Esse comando interrompe a replicação. Você deve contatar as operações da Microsoft para reabilitar a replicação.

No alto nível, aqui estão as etapas para executar um failover de DR:

- Você deve desligar a instância do HANA no site **primário** . Essa ação é necessária apenas se você estiver realmente fazendo o failover para o site de DR para evitar inconsistências de dados.
- Desligue a instância do HANA no nó de recuperação de desastre para o SID de produção.
- Execute o comando `azacsnap -c restore --restore revertvolume` no nó de recuperação de desastre com o Sid a ser recuperado.
  - O comando interrompe o link de replicação de armazenamento do site primário para o local de recuperação de desastre
  - O comando restaura os volumes "dados" e "outros" como configurados.  Normalmente, essa operação seria para os volumes dos sistemas de `/hana/data` Filee `/hana/logbackups` .  O `/hana/shared` sistema de arquivos não é recuperado, mas sim usa o existente `/hana/shared` para Sid no local de recuperação de desastre.
  - Montar os `/hana/data` `/hana/logbackups` volumes e – garantir que eles sejam adicionados ao `/etc/fstab` arquivo
- Restaure o instantâneo do HANA SYSTEMDB. O HANA Studio mostra apenas o instantâneo mais recente do HANA disponível no instantâneo de armazenamento restaurado como parte da execução do comando de instantâneo `azacsnap -c restore --restore revertvolume` .
- Recupere o banco de dados de locatário.
- Inicie a instância do HANA no site de recuperação de desastre para o SID de produção (exemplo: H80 nesse caso).
- Execute qualquer teste de banco de dados.

## <a name="next-steps"></a>Próximas etapas

- [Fazer um backup](azacsnap-cmd-ref-backup.md)
- [Obter detalhes do instantâneo](azacsnap-cmd-ref-details.md)
