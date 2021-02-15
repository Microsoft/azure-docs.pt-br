---
title: Recuperação de desastre usando Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Explica como executar a recuperação de desastres ao usar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730863"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Recuperação de desastre usando Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo explica como executar a recuperação de desastres ao usar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

> [!IMPORTANT]
> Esta operação aplica-se somente ao **Azure instância grande** .

## <a name="introduction"></a>Introdução

A plataforma de instância grande do Azure também pode ter um site de recuperação de desastre configurado no qual os instantâneos de volume de armazenamento podem ser replicados para o.  Se os instantâneos tiverem sido configurados corretamente com essa configuração, será possível executar uma recuperação de desastre neste site.  Este documento destina-se a ser um guia para executar a recuperação de desastres para essa configuração.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Pré-requisitos para a instalação de recuperação de desastre

Os pré-requisitos a seguir devem ser atendidos antes de você planejar o failover de recuperação de desastre.

- Você tem um nó de recuperação de desastres provisionado no local de recuperação de desastre. Há duas opções para DR. Uma é a DR normal e outras são DR multipropósito.
- A replicação de armazenamento está funcionando. A equipe de operações da Microsoft executa a configuração de replicação de armazenamento no momento do provisionamento de recuperação de desastres automaticamente. Você pode monitorar a replicação de armazenamento usando o comando `azacsnap -c details --details replication` no site de recuperação de desastre.
- Você configurou e configurou instantâneos de armazenamento no local principal.
- Você tem uma instância do HANA instalada no site de DR para o primário com o mesmo SID que a instância primária.
- Você lê e entende o procedimento de failover de DR descrito em [SAP Hana em instâncias grandes alta disponibilidade e recuperação de desastre no Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Você configurou e configurou instantâneos de armazenamento no local de recuperação de desastre.
- Um arquivo de configuração (por exemplo, `DR.json` ) foi criado com os volumes de armazenamento de recuperação de desastre e as informações associadas no servidor de recuperação de desastre.
- Você concluiu as etapas no site de recuperação de desastre para:
  - Habilite a comunicação com o armazenamento.
  - Habilite a comunicação com o SAP HANA.

## <a name="set-up-disaster-recovery"></a>Configurar recuperação de desastre

A Microsoft dá suporte à replicação de nível de armazenamento para recuperação de DR. Há duas maneiras de configurar a recuperação de desastre.

Uma é **normal** e outras são **multipropósitos**. Na recuperação de desastre **normal** , você tem uma instância dedicada no local de recuperação de desastres para failover. No cenário de Dr **multipropósito** , você tem outra instância de QA ou de desenvolvimento do Hana em execução na unidade de instância grande do Hana no local de recuperação de desastre. Mas você também instalou uma instância do HANA pré-instalada que está inativa e tem o mesmo SID que a instância do HANA que você deseja fazer failover para essa unidade de instância grande do HANA. As operações da Microsoft configuram o ambiente para você, incluindo a replicação de armazenamento com base na entrada fornecida no formulário de solicitação de serviço (SRF) no momento da integração.

> [!IMPORTANT]
> Verifique se todos os pré-requisitos foram atendidos para a configuração de DR.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Monitorar a replicação de dados do site primário para DR

A equipe de operações da Microsoft já gerencia e monitora o link de recuperação de desastre do site primário para o site de recuperação de desastre.
Você pode monitorar a replicação de dados do servidor primário para o servidor de DR usando o comando de instantâneo `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Executar um failover para um site de DR

Execute o comando failover no local de recuperação de desastres ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> O `azacsnap -c restore --restore revertvolume` comando interrompe a replicação de armazenamento do site de produção para o local de recuperação de desastre. Você deve entrar em contato com as operações da Microsoft para configurar a replicação novamente. Depois que a replicação for reabilitada, todos os dados no armazenamento de DR para esse SID serão inicializados. O comando que executa o failover disponibiliza o instantâneo de armazenamento replicado mais recentemente. Se você precisar restaurar de volta para um instantâneo mais antigo, abra uma solicitação de suporte para que as operações possam ajudar a fornecer um instantâneo anterior restaurado no site de recuperação de desastre.

Em um alto nível, aqui estão as etapas a serem seguidas para o failover de DR:

- Você deve desligar a instância do HANA no site **primário** . Essa ação é necessária apenas se você estiver realmente fazendo o failover para o site de DR para que você não tenha inconsistências de dados.
- Desligue a instância do HANA no nó de recuperação de desastre para o SID de produção.
- Execute o comando `azacsnap -c restore --restore revertvolume` no nó de recuperação de desastre com o Sid a ser recuperado
  - O comando interrompe o link de replicação de armazenamento do site primário para o local de recuperação de desastre
  - O comando restaura o volume/data e/logbackups somente,/Shared volume não é recuperado, mas, em vez disso, ele usa o/Shared existente para SID no local de recuperação de desastres.
  - Montar o volume/data e/logbackups – certifique-se de adicioná-lo ao arquivo fstab
- Restaure o instantâneo do HANA SYSTEMDB. O HANA Studio mostra apenas o instantâneo mais recente do HANA disponível no instantâneo de armazenamento restaurado como parte da execução do comando `azacsnap -c restore --restore revertvolume` .
- Recupere o banco de dados de locatário.
- Inicie a instância do HANA no site de recuperação de desastre para o SID de produção (exemplo: H80 nesse caso).
- Execute os testes.

### <a name="example-performing-disaster-recovery"></a>Exemplo de execução de recuperação de desastre

Esta subseção descreve as etapas detalhadas para um failover para o site de recuperação de desastre.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Etapa 1: obter os detalhes de volume do nó de recuperação de desastre

Execute o comando `df –h` para listar os sistemas de sistema e os volumes associados aos quais fazer referência após o failover.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Etapa 2: desligar o HANA no site primário

Se estiver executando um failover completo de cargas de trabalho de produção e for possível se conectar ao site de produção primário, desligue as instâncias de SAP HANA que passarão por failover para DR.

Por exemplo, se estiver conectado como raiz, o exemplo a seguir mostra como SAP HANA pode ser desligado.  Substitua <sid> pelo SID do SAP Hana.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Etapa 3: desligar o HANA no site de recuperação de desastre

É importante desligar SAP HANA no site de recuperação de desastre antes de restaurar os volumes.

Por exemplo, se estiver conectado como raiz, o exemplo a seguir mostra como SAP HANA pode ser desligado.  Substitua <sid> pelo SID do SAP Hana.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Verifique se as instâncias do HANA no site de recuperação de desastre estão off-line antes de restaurar qualquer volume.

#### <a name="step-4-restore-the-volumes"></a>Etapa 4: restaurar os volumes

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_Saída do comando de failover de recuperação de desastre_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> As etapas no final da exibição do console precisam ser executadas para concluir a preparação de armazenamento para um failover de recuperação de desastre.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Etapa 5: desmontar sistemas de sistema desnecessários

Execute o comando `umount` para desmontar os sistemas de sistema/volumes que não são necessários.

```bash
umount <Mount point>
```

Desmonte os dados e o backup de log montagem. Você pode ter vários mountpoints de dados no cenário de expansão.

#### <a name="step-6-configure-the-mount-points"></a>Etapa 6: configurar os pontos de montagem

Modifique o arquivo `/etc/fstab` para comentar as entradas de backup de dados e de log para o SID primário (neste exemplo, Sid = H80) e adicione as novas entradas de ponto de montagem criadas a partir dos volumes de Dr do site primário. As novas entradas de ponto de montagem são fornecidas na saída do comando.

- Comente os pontos de montagem existentes em execução no site de DR com o `#` caractere:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Adicione as seguintes linhas a `/etc/fstab`
  > deve ser a mesma saída do comando

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Etapa 7: montar os volumes de recuperação

Execute o comando `mount –a` para montar todos os pontos de montagem.

```bash
mount -a
```

Agora, se você executar, `df –h` deverá ver os `*_dp` volumes montados.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Etapa 8: recuperar o SYSTEMDB

No HANA Studio, clique com o botão direito do mouse em instância do SYSTEMDB e escolha "backup e recuperação" e, em seguida, "recuperar banco de dados do sistema"

Consulte o guia para recuperar um banco de dados de um instantâneo, especificamente o SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Etapa 9: recuperar o banco de dados de locatário

No HANA Studio, clique com o botão direito do mouse em instância do SYSTEMDB e escolha "backup e recuperação" e "recuperar banco de dados de locatário".

Consulte o guia para recuperar um banco de dados de um instantâneo, especificamente os bancos de dados de locatário.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Executar `azacsnap -c backup` no local de recuperação de desastre

Se você estiver executando backups baseados em instantâneo no local de recuperação de desastre, o nome do servidor do HANA configurado no `azacsnap` arquivo de configuração no local de recuperação de desastres deverá ser igual ao nome do servidor de produção.

> [!IMPORTANT]
> A execução do `azacsnap -c backup` pode criar instantâneos de armazenamento no local de recuperação de desastres, eles não são replicados automaticamente para outro site.  Trabalhe com operações da Microsoft para entender melhor o retorno de qualquer arquivo ou dados de volta para o site de produção original.

## <a name="next-steps"></a>Próximas etapas

- [Obter detalhes do instantâneo](azacsnap-cmd-ref-details.md)
- [Fazer um backup](azacsnap-cmd-ref-backup.md)
