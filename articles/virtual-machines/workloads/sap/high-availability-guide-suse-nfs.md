---
title: Alta disponibilidade para NFS em VMs do Azure no SLES | Microsoft Docs
description: Alta disponibilidade do NFSVMs do Azure no SUSE Linux Enterprise Server
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 993baa521530ffa6a702f8324a1691850687c366
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668683"
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


> [!NOTE]
> Este artigo contém referências aos termos *subordinado* e *mestre*, termos que a Microsoft não usa mais. Quando os termos forem removidos do software, nós os removeremos deste artigo.

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
* [Implantação de Máquinas Virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Guias de melhores práticas do SUSE Linux Enterprise High Availability Extension 12 SP3][sles-hae-guides]
  * Armazenamento NFS Altamente Disponível com DRBD e Pacemaker
* [Guias de melhores práticas do SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp]
* [Notas sobre a versão do SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

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
* Regras de balanceamento de carga (se estiver usando o Load Balancer básico)
  * TCP 2049 para NW1
  * UDP 2049 para NW1
  * TCP 2049 para NW2
  * UDP 2049 para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS altamente disponível

Você pode usar um Modelo do Azure do GitHub para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e balanceador de carga ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do Modelo do Azure

O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais.
Você pode usar um dos modelos de início rápido no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo de servidor de arquivo do SAP][template-file-server] no Portal do Azure   
1. Defina os seguintes parâmetros
   1. Prefixo de recursos  
      Digite o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que serão implantados.
   2. Contagem do sistema SAP  
      Insira o número de sistemas SAP que usará esse servidor de arquivos. Isso implantará a quantidade necessária de configurações de front-end, regras de balanceamento de carga, portas de investigação, discos, etc.
   3. Tipo de sistema operacional  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12
   4. Nome de Usuário de Administrador e Senha do Administrador  
      É criado um novo usuário que pode ser usado para fazer logon no computador.
   5. ID da Sub-rede  
      Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente se parece com a **&lt; ID &gt; da assinatura**/subscriptions//resourceGroups/**&lt; &gt;****&lt; &gt;** nome do grupo de recursos,/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual/Subnets/**&lt; nome &gt; da sub-rede**

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
1. Crie um Load Balancer (interno). Recomenda-se o [Standard Load Balancer](../../../load-balancer/load-balancer-overview.md).  
   1. Siga estas instruções para criar o balanceador de carga padrão:
      1. Criar os endereços IP de front-end
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
            1. Digite o nome do novo pool de IPs de front-end (por exemplo **nw1-frontend**)
            1. Defina a Atribuição ao Estático e digite o endereço IP (por exemplo **10.0.0.4**)
            1. Clique em OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita as etapas acima para a porta NW2
      1. Criar os pools de back-end
         1. Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster NFS
            1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
            1. Insira o nome do novo pool de back-end (por exemplo **, NW-back-end**)
            1. Selecionar rede virtual
            1. Clique em Adicionar uma máquina virtual
            1. Selecione as máquinas virtuais do cluster NFS e seus endereços IP.
            1. Clique em Adicionar.
      1. Crie as investigações de integridade
         1. Porta 61000 para NW1
            1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
            1. Digite o nome da nova investigação de integridade (por exemplo, **nw1-hp**)
            1. Escolha TCP como protocolo, porta 610 **00**, mantenha o Intervalo como 5 e o Limite de não integridade como 2
            1. Clique em OK
         1. Porta 61001 para NW2
            * Repita as etapas acima para criar uma investigação de integridade para NW2
      1. Regras de balanceamento de carga
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo **, NW1-lb**)
         1. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo **NW1-frontend**. **NW-backend** e **NW1-HP**)
         1. Selecione **Portas de HA**.
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Habilite o IP Flutuante**
         1. Clique em OK
         * Repita as etapas acima para criar a regra de balanceamento de carga para NW2
   1. Como alternativa, se seu cenário exigir o Load Balancer básico, siga estas instruções:
      1. Criar os endereços IP de front-end
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
            1. Digite o nome do novo pool de IPs de front-end (por exemplo **nw1-frontend**)
            1. Defina a Atribuição ao Estático e digite o endereço IP (por exemplo **10.0.0.4**)
            1. Clique em OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita as etapas acima para a porta NW2
      1. Criar os pools de back-end
         1. Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster NFS
            1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
            1. Insira o nome do novo pool de back-end (por exemplo **, NW-back-end**)
            1. Clique em Adicionar uma máquina virtual
            1. Selecione o Conjunto de Disponibilidade criado anteriormente
            1. Selecione as máquinas virtuais do cluster NFS
            1. Clique em OK
      1. Crie as investigações de integridade
         1. Porta 61000 para NW1
            1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
            1. Digite o nome da nova investigação de integridade (por exemplo, **nw1-hp**)
            1. Escolha TCP como protocolo, porta 610 **00**, mantenha o Intervalo como 5 e o Limite de não integridade como 2
            1. Clique em OK
         1. Porta 61001 para NW2
            * Repita as etapas acima para criar uma investigação de integridade para NW2
      1. Regras de balanceamento de carga
         1. TCP 2049 para NW1
            1. Clique no balanceador de carga, escolha as regras de balanceamento de carga e clique em Adicionar
            1. Insira o nome da nova regra de balanceador de carga (por exemplo **nw1-lb-2049**)
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
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.  

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, confira [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas subjacentemente ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, confira [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

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

1. **[A]** Habilitar o servidor NFS

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

1. **[A]** Configurar drbd

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

1. **[1]** Definir o nó principal

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

1. **[A]** Configuração da detecção de divisão de dupla personalidade drbd

   Ao usar o drbd para sincronizar os dados de um host para outro, pode ocorrer a chamada divisão de dupla personalidade. Uma divisão Brain é um cenário em que ambos os nós de cluster promoveram o dispositivo DRBD para ser o primário e não foram sincronizados. Pode ser uma situação rara, mas você ainda deseja manipular e resolver uma divisão de cabeça o mais rápido possível. Portanto, é importante ser notificado quando ocorrer uma divisão de dupla personalidade.

   Leitura [da documentação oficial drbd](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) sobre como configurar uma notificação de dados de dupla personalidade.

   Também é possível recuperar automaticamente a partir de um cenário de divisão de dupla personalidade. Para obter mais informações, leia [Políticas de recuperação de dupla personalidade automática](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar a estrutura do cluster

1. **[1]** Adicionar o dispositivo de drbd do NFS ao NW1 do sistema para a configuração do cluster

   > [!IMPORTANT]
   > Testes recentes revelaram situações em que o netcat deixa de responder às solicitações devido à lista de pendências e à limitação dele de manipular apenas uma conexão. O recurso netcat deixa de escutar as solicitações do Azure Load Balancer e o IP flutuante fica não disponível.  
   > Para os clusters existentes do Pacemaker, recomendamos a substituição do netcat pelo socat. No momento, é recomendável usar o agente de recursos do azure-lb, que faz parte do pacote resource-agents, com os seguintes requisitos de versão do pacote:
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Para o SLES 15/15 SP1, a versão deve ser pelo menos resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Observe que a alteração exigirá um breve tempo de inatividade.  
   > Para clusters do Pacemaker existentes, se a configuração já foi alterada para usar socat conforme descrito em [Proteção de Detecção do Azure Load Balancer](https://www.suse.com/support/kb/doc/?id=7024128), não há nenhum requisito para mudar imediatamente para o agente de recursos azure-lb.

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
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
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
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   A `crossmnt` opção nos `exportfs` recursos de cluster está presente em nossa documentação para compatibilidade com versões anteriores do SLES.  

1. **[1]** Desabilitar o modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Instale o SAP ASCS e o banco de dados](high-availability-guide-suse.md)
* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
