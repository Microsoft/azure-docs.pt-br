---
title: Alta disponibilidade do NFSVMs do Azure no SUSE Linux Enterprise Server | Microsoft Docs
description: Alta disponibilidade do NFSVMs do Azure no SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: a91bc1cbb72427205cc558a4b5e655f4aa8083b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710704"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade do NFSVMs do Azure no SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um servidor NFS com alta disponibilidade que pode ser usado para armazenar dados compartilhados de um sistema SAP altamente disponível.
Este guia descreve como configurar um servidor NFS altamente disponível que é usado pelos dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo pressupõem que você tenha usado o [modelo de servidor de arquivo SAP][template-file-server] com o **prod** de prefixo de recurso.

Primeiro, leia os seguintes documentos e Notas SAP

* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure

* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2205917] tem configurações de SO recomendadas para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Guias de melhores práticas do SUSE Linux Enterprise High Availability Extension 12 SP3][sles-hae-guides]
  * Armazenamento NFS Altamente Disponível com DRBD e Pacemaker
* [Guias de melhores práticas do SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp]
* [Notas de versão do SP3 com a extensão de alta disponibilidade do SUSE 12][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Visão geral

Para obter alta disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado em um cluster separado e pode ser usado por vários sistemas SAP.

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

O servidor usa um nome de host virtual dedicado e endereços IP virtuais para todos os sistemas SAP que usam esse servidor NFS. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga.        

* Configuração de front-end
  * Endereço IP 10.0.0.4 para NW1
  * Endereço IP 10.0.0.5 para NW2
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster NFS
* Porta de Investigação
  * Porta 61000 para NW1
  * Porta 61001 para NW2
* Regras de balanceamento de carga
  * TCP 2049 para NW1
  * UDP 2049 para NW1
  * TCP 2049 para NW2
  * UDP 2049 para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS altamente disponível

Você pode usar um Modelo do Azure do GitHub para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e balanceador de carga ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do Modelo do Azure

O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais.
Você pode usar um dos modelos de início rápido no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo de servidor de arquivo do SAP][template-file-server] no Portal do Azure   
1. Defina os seguintes parâmetros
   1. Prefixo de recursos  
      Digite o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que serão implantados.
   2. Contagem do sistema SAP  
      Insira o número de sistemas SAP que usará esse servidor de arquivos. Isso implantará a quantidade necessária de configurações de front-end, regras de balanceamento de carga, de portas de investigação, discos etc.
   3. Tipo de sistema operacional  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12
   4. Nome de Usuário de Administrador e Senha do Administrador  
      É criado um novo usuário que pode ser usado para fazer logon no computador.
   5. ID da Sub-rede  
      Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente tem esta aparência: /subscriptions/**&lt;ID da assinatura&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nome de rede virtual&gt;**/subnets/**&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar as máquinas virtuais para este cluster NFS. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais nos pools de back-end.

1. Criar um grupo de recursos
1. Criar uma rede virtual
1. Crie um Conjunto de Disponibilidade  
   Defina o máximo de domínio de atualização
1. Criar Máquina Virtual 1 Use pelo menos SLES4SAP 12 SP3, neste exemplo, LES4SAP 12 SP3 BYOS imagem SLES Para aplicativos 12 SP3 (BYOS) é usado  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Criar Máquina Virtual 2 Use no mínimo SLES4SAP 12 SP3, neste exemplo, a imagem SLES4SAP 12 SP3 BYOS  
   O SLES For SAP Applications 12 SP3 (BYOS) é usado  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Adicione um disco de dados para cada sistema SAP para ambas as máquinas virtuais.
1. Crie um Balanceador de Carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.4 para NW1
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Digite o nome do novo pool de IPs de front-end (por exemplo **nw1-frontend**)
         1. Defina a Atribuição ao Estático e digite o endereço IP (por exemplo **10.0.0.4**)
         1. Clique em OK
      1. Endereço IP 10.0.0.5 para NW2
         * Repita as etapas acima para a porta NW2
   1. Criar os pools de back-end
      1. Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster NFS para NW1
         1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
         1. Digite o nome do novo pool de back-end (por exemplo **nw1-backend**)
         1. Clique em Adicionar uma máquina virtual
         1. Selecione o Conjunto de Disponibilidade criado anteriormente
         1. Selecione as máquinas virtuais do cluster NFS
         1. Clique em OK
      1. Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster NFS para NW2
         * Repita as etapas acima para criar um pool de back-end para NW2
   1. Crie as investigações de integridade
      1. Porta 61000 para NW1
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Digite o nome da nova investigação de integridade (por exemplo, **nw1-hp**)
         1. Escolha TCP como protocolo, porta 610**00**, mantenha o Intervalo como 5 e o Limite de não integridade como 2
         1. Clique em OK
      1. Porta 61001 para NW2
         * Repita as etapas acima para criar uma investigação de integridade para NW2
   1. Regras de balanceamento de carga
      1. TCP 2049 para NW1
         1. Clique no balanceador de carga, escolha as regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra de balanceador de carga (por exemplo  **nw1-lb-2049**)
         1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo **, nw1-frontend**)
         1. Mantenha o protocolo **TCP**, insira a porta **2049**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Habilite o IP Flutuante**
         1. Clique em OK
      1. UDP 2049 para NW1
         * Repita as etapas acima para a porta 2049 e UDP para NW1
      1. TCP 2049 para NW2
         * Repita as etapas acima para a porta 2049 e TCP para NW2
      1. UDP 2049 para NW2
         * Repita as etapas acima para a porta 2049 e UDP para NW2

> [!IMPORTANT]
> Não habilite os carimbos de hora do TCP em VMs do Azure colocadas por trás do balanceador de carga do Azure. Habilitar TCP carimbos de hora fará com que as investigações de integridade falha. Defina o parâmetro **net.ipv4.tcp_timestamps** à **0**. Para obter detalhes, consulte [investigações de integridade do balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster Pacemaker básico para esse servidor NFS.

### <a name="configure-nfs-server"></a>Configurar o servidor NFS

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Habilitar o servidor NFS

   Criar a entrada de exportação NFS de raiz

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Instalar componentes drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Criar uma partição para os dispositivos drbd

   Relacionar todos os discos disponíveis

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Saída de exemplo
   
   ```
   lun0  lun1
   ```

   Criar partições para cada disco de dados

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Criar configurações de LVM

   Relacionar todos as particições disponíveis

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Saída de exemplo
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Criar volumes LVM para cada partição

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Configurar drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Certifique-se de que o arquivo drbd.conf contém as duas linhas a seguir

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Altere a configuração global drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adicione as seguintes entradas para o manipulador e seção net.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** Crie os dispositivo drbd do NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e sair

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e sair

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Criar o dispositivo drbd e iniciá-lo

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ignorar a sincronização inicial

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Definir o nó principal

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Aguardar até que os novos dispositivos drbd sejam sincronizados

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Criar sistemas de arquivos nos dispositivos drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Configuração da detecção de divisão de dupla personalidade drbd

   Ao usar o drbd para sincronizar os dados de um host para outro, pode ocorrer a chamada divisão de dupla personalidade. Uma divisão de dupla personalidade é um cenário em que ambos os nós de cluster promoveram o dispositivo drbd para ser o principal e saíram de sincronização. Pode ser uma situação rara, mas você ainda vai desejar controlar e resolver uma divisão de dupla personalidade o mais rápido possível. Portanto, é importante ser notificado quando ocorrer uma divisão de dupla personalidade.

   Leitura [da documentação oficial drbd](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) sobre como configurar uma notificação de dados de dupla personalidade.

   Também é possível recuperar automaticamente a partir de um cenário de divisão de dupla personalidade. Para obter mais informações, leia [Políticas de recuperação de dupla personalidade automática](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar a estrutura do cluster

1. **[1]** Adicionar o dispositivo de drbd do NFS ao NW1 do sistema para a configuração do cluster

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Adicionar o dispositivo de drbd do NFS ao NW2 do sistema para a configuração do cluster

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** Desabilitar o modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Instale o SAP ASCS e o banco de dados](high-availability-guide-suse.md)
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
