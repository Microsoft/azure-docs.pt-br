---
title: Configuração do Pacemaker em SLES no Azure | Microsoft Docs
description: Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: ddee5edcf1d19af0fb088976c590b62866a1484e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674418"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Há duas opções para configurar um cluster Pacemaker no Azure. Você pode usar um agente de isolamento que cuida da reinicialização de um nó com falha por meio das APIs do Azure ou você pode usar um dispositivo SBD.

O dispositivo SBD requer pelo menos uma máquina virtual adicional que atue como um servidor de destino iSCSI e forneça um dispositivo SBD. Esses servidores de destino iSCSI, podem, entretanto, ser compartilhados com outros clusters do Pacemaker. A vantagem de usar um dispositivo SBD é, se você já estiver usando dispositivos SBD no local, o não exigirá nenhuma alteração na maneira como você opera o cluster pacemaker. Você pode usar até três dispositivos SBD para um cluster do Pacemaker para permitir que um dispositivo SBD se torne indisponível, por exemplo, durante a aplicação de patch de sistema operacional do servidor de destino iSCSI. Se você quiser usar mais de um dispositivo SBD por Pacemaker, certifique-se implantar vários servidores de destino iSCSI e conectar um SBD de cada servidor de destino iSCSI. É recomendável usar um dispositivo SBD ou três. O Pacemaker não poderá automaticamente isolar um nó de cluster se você apenas configurar dois dispositivos SBD e um deles não estiver disponível. Se você quiser poder Defence-lo quando um servidor de destino iSCSI estiver inativo, precisará usar três dispositivos SBD e, portanto, três servidores de destino iSCSI, que é a configuração mais resiliente ao usar o SBDs.

O agente de limite do Azure não requer a implantação de máquinas virtuais adicionais.   

![Pacemaker na visão geral do SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Ao planejar e implantar nós de cluster do Linux Pacemaker e dispositivos SBD, é essencial para a confiabilidade geral da configuração completa do cluster que o roteamento entre as VMs envolvidas e a(s) VM(s) que hospeda(m) o(s) dispositivo(s) SBD não esteja passando por nenhum outro dispositivos como [NVAs](https://azure.microsoft.com/solutions/network-appliances/). Caso contrário, problemas e eventos de manutenção com o NVA podem ter um impacto negativo na estabilidade e confiabilidade da configuração geral do cluster. Para evitar esses obstáculos, não defina regras de roteamento de NVAs ou [regras de Roteamento Definido pelo Usuário](../../../virtual-network/virtual-networks-udr-overview.md) que roteiem o tráfego entre nós de cluster e dispositivos SBD por meio de NVAs e dispositivos semelhantes ao planejar e implantar nós de cluster do Linux Pacemaker e dispositivos SBD. 
>

## <a name="sbd-fencing"></a>Isolamento de SBD

Siga etapas a seguir se você quiser usar um dispositivo SBD para isolamento.

### <a name="set-up-iscsi-target-servers"></a>Configurar servidores de destino iSCSI

Primeiro, você precisa criar as máquinas virtuais de destino iSCSI. Os servidores de destino iSCSI podem ser compartilhados com vários clusters Pacemaker.

1. Implante um novo SLES 12 SP1 ou máquinas virtuais superiores e conecte-se a eles por meio do ssh. As máquinas não precisam ser grandes. Um tamanho de máquina virtual, como Standard_E2s_v3 ou Standard_D2s_v3 é suficiente. Certifique-se de usar o armazenamento Premium do disco do SO.

Execute os seguintes comandos em todas as **máquinas virtuais de destino iSCSI**.

1. Atualizar o SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Talvez seja necessário reinicializar o sistema operacional depois de fazer upgrade ou atualizar o sistema operacional. 

1. Remover pacotes

   Para evitar um problema conhecido com targetcli e SLES 12 SP3, desinstale os pacotes a seguir. Você pode ignorar erros sobre os pacotes que não podem ser encontrados

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instale os pacotes de destino iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Habilite o serviço de destino iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Crie o dispositivo iSCSI no servidor de destino iSCSI

Execute os seguintes comandos em todas as **máquinas virtuais de destino iSCSI** para criar os discos iSCSI para os clusters usados por seus sistemas SAP. No exemplo a seguir, os dispositivos SBD para vários clusters são criados. Ele mostra como você usaria um servidor de destino iSCSI para vários clusters. Os dispositivos SBD são colocados no disco do SO. Certifique-se de que você tenha espaço suficiente.

**`nfs`** é usado para identificar o cluster de NFS **ascsnw1** é usado para identificar o cluster de ASCS **NW1**, **dbnw1** é usado para identificar o cluster de banco de dados de **NW1**, **nfs-0** e **nfs-1** são os nomes de host dos nós de cluster NFS,**nw1-xscs-0** e **nw1-xscs-1** são os nomes de host dos nós de cluster ASCS de **NW1** e **nw1-db-0** e **nw1-db-1** são os nomes de host dos nós de cluster de banco de dados. Substitua-os pelos nomes do hoste do seus nós de cluster e pelo SID do sistema SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Você pode verificar se tudo foi configurado corretamente com

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Configurar o dispositivo SBD

Conecte-se ao dispositivo iSCSI que foi criado na última etapa do cluster.
Execute os seguintes comandos nos nós do novo cluster que você deseja criar.
Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]**  Conecte aos dispositivos iSCSI

   Primeiro, habilite o iSCSI e os serviços SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Altere o nome do iniciador no primeiro nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do arquivo para corresponder as ACLs que você usou ao criar o dispositivo iSCSI no servidor de destino iSCSI, por exemplo, para o servidor NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Altere o nome do iniciador no segundo nó

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do arquivo para corresponder as ACLs que você usou ao criar o dispositivo iSCSI no servidor de destino iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Reiniciar o serviço iSCSI

   Agora, reinicie o serviço iSCSI para aplicar a alteração

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Conecte aos dispositivos iSCSI. No exemplo a seguir, 10.0.0.17 é o endereço IP do servidor de destino iSCSI e 3260 é a porta padrão. <b>iqn.2006 04.nfs.local:nfs</b> é um dos nomes de destino que é listado quando você executa o primeiro comando abaixo (descoberta de iscsiadm -m).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Certifique-se de que o dispositivo iSCSI esteja disponível e observe o nome do dispositivo (no seguinte exemplo, /des/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Agora, recupere as IDs dos dispositivos iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   O comando lista três IDs de dispositivo para cada dispositivo SBD. É recomendável usar a ID que começa com scsi-3, no exemplo acima é

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Criar o dispositivo SBD

   Use a ID do dispositivo dos dispositivos iSCSI para criar os novos dispositivos SBD no primeiro nó do cluster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Adaptar a configuração SBD

   Abra o arquivo de configuração SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Altere a propriedade do dispositivo SBD, habilite a integração do pacemaker e altere o modo de início de SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Criar o arquivo de configuração `softdog`

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Agora, carregue o módulo

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalação do Cluster

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Atualizar o SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Instalar o componente, necessário para recursos de cluster

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Instalar o componente azure-lb, necessário para recursos de cluster

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Verifique a versão do pacote resource-agents e certifique-se de que os requisitos mínimos de versão sejam atendidos:  
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Para o SLES 15/15 SP1, a versão deve ser pelo menos resource-agents-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Configurar o sistema operacional

   Em alguns casos, o Pacemaker cria muitos processos e esgota, assim, o número permitido de processos. Nesse caso, uma pulsação entre os nós de cluster pode falhar e levar a failover de seus recursos. Recomendamos aumentar o máximo permitido de processos definindo o parâmetro a seguir.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Reduza o tamanho do cache sujo. Para obter mais informações, consulte [Baixo desempenho de gravação em servidores SLES 11/12 com RAM grande](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configurar cloud-netconfig-azure para cluster de alta disponibilidade

   >[!NOTE]
   > Verifique a versão instalada do pacote **cloud-netconfig-azure** executando **zypper info cloud-netconfig-azure**. Se a versão em seu ambiente for 1.3 ou superior, não será mais necessário suprimir o gerenciamento de interfaces de rede pelo plug-in de rede de nuvem. Se a versão for inferior a 1.3, sugerimos que você atualize o pacote **cloud-netconfig-azure** para a versão mais recente disponível.  

   Altere o arquivo de configuração para a interface de rede, conforme mostrado abaixo para impedir que o plug-in de rede de nuvem remova o endereço IP virtual (Pacemaker deve controlar a atribuição de VIP). Para obter mais informações, confira [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Habilitar o acesso ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Habilitar o acesso ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Habilitar o acesso ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** instale o pacote de agentes de isolamento, se estiver usando o dispositivo STONITH, com base no agente de isolamento do Azure.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > A versão instalada do limite do pacote **-agentes** deve ser pelo menos **4.4.0**  para se beneficiar dos tempos de failover mais rápidos com o agente de isolamento do Azure, se um nó de cluster precisar ser degradedo. Recomendamos que você atualize o pacote, se estiver executando uma versão inferior.  


1. **[A]** instalar o SDK do Python do Azure 
   - No SLES 12 SP4 ou SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - No SLES 15 e superior 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >Dependendo de sua versão e tipo de imagem, talvez seja necessário ativar a extensão de nuvem pública para sua versão do sistema operacional, antes de instalar o SDK do Python do Azure.
   >Você pode verificar a extensão, executando SUSEConnect---List-Extensions.  
   >Para obter os tempos de failover mais rápidos com o agente de isolamento do Azure:
   > - no SLES 12 SP4 ou SLES 12 SP5, instale a versão **4.6.2** ou superior do pacote Python-Azure-MGMT-Compute  
   > - no SLES 15, instale a versão **4.6.2** ou superior do pacote Python **3**-Azure-MGMT-Compute 

1. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir.

   >[!IMPORTANT]
   > Se estiver usando nomes de host na configuração do cluster, é vital ter a resolução de nome de host confiável. A comunicação do cluster falhará se os nomes não estiverem disponíveis e isso pode levar a atrasos de failover de cluster.
   > A vantagem de usar /etc/hosts é que o cluster se torna independente do DNS, o que também pode ser um ponto único de falhas.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instalar o Cluster
- Se estiver usando dispositivos SBD para isolamento
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- Se *não estiver usando* dispositivos SBD para isolamento
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Adicionar nó ao cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Alterar a senha hacluster para a mesma senha

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Ajustar as configurações de corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adicione o seguinte conteúdo em negrito para o arquivo se os valores não forem diferentes ou estiverem ausentes. Altere o token para 30.000 para permitir a manutenção da preservação da Memória. Para obter mais informações, consulte [este artigo para Linux][virtual-machines-linux-maintenance] ou [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Em seguida, reinicie o serviço corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configuração do Pacemaker padrão para SBD

A configuração nesta seção só será aplicável se você estiver usando SBD STONITH.  

1. **[1]**  Permitir o uso de um dispositivo STONITH e definir o atraso de isolamento

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Criar dispositivo STONITH de agente do Azure Fence

Esta seção da documentação só será aplicável se você estiver usando o STONITH, com base no agente de isolamento do Azure.
O dispositivo STONITH usa uma Entidade de Serviço para autorização no Microsoft Azure. Siga estas etapas para criar uma Entidade de Serviço.

1. Acesse <https://portal.azure.com>
1. Abra a folha Azure Active Directory  
   Vá para Propriedades e anote a ID do Diretório. Essa é a **ID de locatário**.
1. Clique em Registros do Aplicativo
1. Clique em Novo registro
1. Insira um Nome, selecione "Contas apenas neste diretório de organização" 
2. Selecione um Tipo de Aplicativo "Web", insira uma URL de logon (por exemplo http:\//localhost) e clique em Adicionar  
   A URL de logon não é usada e pode ser qualquer URL válida
1. Selecione Certificados e Segredos e clique em Novo segredo do cliente
1. Insira uma descrição para uma nova chave, selecione "Nunca expira" e clique em Adicionar
1. Anote o Valor. Ele é usado como **senha** da Entidade de Serviço
1. Selecione Visão geral. Anote a ID do Aplicativo. Ela é usada como nome de usuário (**ID de logon** nas etapas abaixo) da Entidade de Serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Criar uma função personalizada para o agente de isolamento

A Entidade de Serviço não tem permissões para acessar os recursos do Azure por padrão. Você precisa fornecer as permissões da Entidade de Serviço para iniciar e parar (desalocar) todas as máquinas virtuais do cluster. Se você ainda não tiver criado a função personalizada, você pode criá-la usando o [PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) ou [CLI do Azure](../../../role-based-access-control/custom-roles-cli.md)

Use o seguinte conteúdo para o arquivo de entrada. Você precisa adaptar o conteúdo às suas assinaturas, ou seja, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 pelas IDs da sua assinatura. Se você tiver apenas uma assinatura, remova a segunda entrada em AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Atribuir a função personalizada à Entidade de Serviço

Atribua a função personalizada “Função do Agente de Isolamento Linux" que foi criada no último capítulo para a Entidade de Serviço. Não mais use a função de proprietário!

1. Vá até [https://portal.azure.com](https://portal.azure.com)
1. Abra a folha Todos os recursos
1. Selecione a máquina virtual do primeiro nó do cluster
1. Clique em Controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função "Função do Agente de Isolamento do Linux”
1. Digite o nome do aplicativo criado acima
1. Clique em Salvar

Repita as etapas acima para o segundo nó do cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Criar os dispositivos STONITH

Depois de editar as permissões das máquinas virtuais, você pode configurar os dispositivos STONITH no cluster.

> [!NOTE]
> A opção ' pcmk_host_map ' só será necessária no comando se os nomes de host e os nomes de VM do Azure não forem idênticos. Especifique o mapeamento no formato **hostname: VM-Name**.
> Consulte a seção em negrito no comando.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> As operações de monitoramento e isolamento são desserializadas. Como resultado, se houver uma operação de monitoramento em execução mais longa e um evento de isolamento simultâneo, não haverá atraso para o failover de cluster, devido à operação de monitoramento já em execução.

> [!TIP]
>O agente do Azure Fence requer conectividade de saída para pontos de extremidade públicos, conforme documentado, juntamente com soluções possíveis, em [Conectividade de ponto de extremidade pública para VMs usando o ILB padrão](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Configuração do pacemaker para eventos agendados do Azure

O Azure oferece [eventos agendados](../../linux/scheduled-events.md). Os eventos agendados são fornecidos por meio do serviço de metadados e permitem tempo para o aplicativo se preparar para eventos como desligamento de VM, reimplantação de VM etc. O agente de recursos **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitora os eventos agendados do Azure. Se forem detectados eventos, o agente tentará interromper todos os recursos na VM afetada e movê-los para outro nó no cluster. Para obter os recursos adicionais do Pacemaker, é necessário configurá-los. 

1. **[A]** Verifique se o pacote do agente **azure-events** já está instalado e atualizado. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Configure os recursos no Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Depois de configurar os recursos do Pacemaker para o agente azure-events, ao colocar o cluster dentro ou fora do modo de manutenção, você poderá receber mensagens de aviso como:  
     AVISO: cib-bootstrap-options: atributo desconhecido 'hostName_  <strong>hostname</strong>'  
     AVISO: cib-bootstrap-options: atributo desconhecido 'azure-events_globalPullState'  
     AVISO: cib-bootstrap-options: atributo desconhecido 'hostName_ <strong>hostname</strong>'  
   > Essas mensagens de aviso podem ser ignoradas.

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server][sles-nfs-guide]
* [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP][sles-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
