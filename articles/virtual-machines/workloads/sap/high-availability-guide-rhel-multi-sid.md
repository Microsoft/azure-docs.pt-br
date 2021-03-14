---
title: Alta disponibilidade de VMs do Azure para SAP NW no RHEL multi-SID guia | Microsoft Docs
description: Estabelecer alta disponibilidade para o SAP NW em VMs (máquinas virtuais) do Azure RHEL multi-SID.
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
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676925"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Alta disponibilidade para SAP NetWeaver em VMs do Azure em Red Hat Enterprise Linux para aplicativos SAP guia de vários SIDs

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar vários sistemas de alta disponibilidade do SAP NetWeaver (ou seja, vários SIDs) em um cluster de dois nós em VMs do Azure com Red Hat Enterprise Linux para aplicativos SAP.  

Nas configurações de exemplo, comandos de instalação etc. três sistemas SAP NetWeaver 7,50 são implantados em um único cluster de alta disponibilidade de dois nós. Os SIDs de sistemas SAP são:
* **NW1**: número de instância de ASCS **00** e nome de host virtual **msnw1ascs**; Número de instância ERS **02** e nome de host virtual **msnw1ers**.  
* **NW2**: ASCS da instância número **10** e nome do host virtual **msnw2ascs**; ERS instância número **12** e nome de host virtual **msnw2ers**.  
* **NW3**: ASCS da instância número **20** e nome do host virtual **msnw3ascs**; Número de instância ERS **22** e nome de host virtual **msnw3ers**.  

O artigo não aborda a camada de banco de dados e a implantação dos compartilhamentos NFS do SAP. Nos exemplos deste artigo, estamos usando [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  volume **sapMSID** para os compartilhamentos NFS, supondo que o volume já esteja implantado. Também presumimos que o volume Azure NetApp Files seja implantado com o protocolo NFSv3 e que os seguintes caminhos de arquivo existam para os recursos de cluster para as instâncias ASCS e ERS dos sistemas SAP NW1, NW2 e NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ASCs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ERS)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ASCs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ERS)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ASCs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ERS)

Antes de começar, consulte as seguintes notas e documentos do SAP primeiro:

* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure
* [Documentação do Azure NetApp Files][anf-azure-doc]
* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* Nota SAP [2002167] recomendou configurações do sistema operacional Red Hat Enterprise Linux
* Nota SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [SAP Netweaver no cluster do pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurando o ASCS/ERS do SAP Netweaver com recursos autônomos no RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurar o SAP S/4HANA ASCS/ERS com o servidor de enfileiramento autônomo 2 (ENSA2) no pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Visão geral

As máquinas virtuais, que participam do cluster, devem ser dimensionadas para poder executar todos os recursos, caso ocorra um failover. Cada SID do SAP pode fazer failover de forma independente uma da outra no cluster de alta disponibilidade de vários SIDs.  

Para obter alta disponibilidade, o SAP NetWeaver requer compartilhamentos altamente disponíveis. Nesta documentação, apresentamos os exemplos com os compartilhamentos do SAP implantados em [volumes Azure NetApp files NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Também é possível hospedar os compartilhamentos no [cluster GlusterFS](./high-availability-guide-rhel-glusterfs.md)altamente disponível, que pode ser usado por vários sistemas SAP.  

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> O suporte para clustering de vários SIDs do SAP ASCS/ERS com Red Hat Linux como sistema operacional convidado em VMs do Azure é limitado a **cinco** SIDs do SAP no mesmo cluster. Cada novo SID aumenta a complexidade. **Não há suporte para** uma combinação do servidor de replicação de enfileiramento do SAP 1 e do servidor de replicação de enfileiramento 2 no mesmo cluster. O clustering de vários SID descreve a instalação de várias instâncias do SAP ASCS/ERS com SIDs diferentes em um cluster pacemaker. Atualmente, o clustering de vários SIDs só tem suporte para ASCS/ERS.  

> [!TIP]
> O clustering de vários SIDs do SAP ASCS/ERS é uma solução com maior complexidade. É mais complexo implementar. Ele também envolve um esforço administrativo maior, ao executar atividades de manutenção (como aplicação de patch de so). Antes de iniciar a implementação real, Reserve um tempo para planejar cuidadosamente a implantação e todos os componentes envolvidos, como VMs, montagens NFS, VIPs, configurações do balanceador de carga e assim por diante.  

O SAP NetWeaver ASCS, o SAP NetWeaver SCS e o SAP NetWeaver ERS usam o nome de host virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. É recomendável usar o [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

A lista a seguir mostra a configuração do (a) SCS e ERS Load Balancer para este exemplo de cluster de vários SID com três sistemas SAP. Será necessário um IP de front-end separado, investigações de integridade e regras de balanceamento de carga para cada instância de ASCS e ERS para cada um dos SIDs. Atribua todas as VMs que fazem parte do cluster ASCS/ASCS a um pool de back-end de um único ILB.  

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP para NW1:10.3.1.50
  * Endereço IP para NW2:10.3.1.52
  * Endereço IP para NW3:10.3.1.54

* Portas de investigação
  * Porta 620 <strong> &lt; NR &gt;</strong>, portanto para as portas de investigação NW1, NW2 e NW3 620 **00**, 620 **10** e 620 **20**
* Regras de balanceamento de carga – crie uma para cada instância, ou seja, NW1/ASCS, NW2/ASCS e NW3/ASCS.
  * Se estiver usando o Standard Load Balancer, selecione **portas de HA**
  * Se estiver usando o Load Balancer Básico, crie regras de balanceamento de carga para as portas a seguir
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP para NW1 10.3.1.51
  * Endereço IP para NW2 10.3.1.53
  * Endereço IP para NW3 10.3.1.55

* Porta de Investigação
  * Porta 621 <strong> &lt; NR &gt;</strong>, portanto para as portas de investigação NW1, NW2 e N3 621 **02**, 621 **12** e 621 **22**
* Regras de balanceamento de carga – crie uma para cada instância, ou seja, NW1/ERS, NW2/ERS e NW3/ERS.
  * Se estiver usando o Standard Load Balancer, selecione **portas de HA**
  * Se estiver usando o Load Balancer Básico, crie regras de balanceamento de carga para as portas a seguir
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.  

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, confira [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas subjacentemente ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, veja [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-shares"></a>Compartilhamentos SAP

O SAP NetWeaver requer armazenamento compartilhado para o transporte, o diretório de perfil e assim por diante. Para o sistema SAP altamente disponível, é importante ter compartilhamentos altamente disponíveis. Você precisará decidir sobre a arquitetura de seus compartilhamentos do SAP. Uma opção é implantar os compartilhamentos em [volumes Azure NetApp files NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Com o Azure NetApp Files, você terá alta disponibilidade interna para os compartilhamentos NFS do SAP.

Outra opção é criar [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), que pode ser compartilhado entre vários sistemas SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Implantar o primeiro sistema SAP no cluster

Agora que você decidiu sobre a arquitetura dos compartilhamentos do SAP, implante o primeiro sistema SAP no cluster, seguindo a documentação correspondente.

* Se estiver usando Azure NetApp Files volumes de NFS, siga a [alta disponibilidade de VMs do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md)  
* Se estiver usando o cluster GlusterFS, siga [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

Os documentos listados acima orientarão você pelas etapas para preparar a infraestrutura necessária, criar o cluster, preparar o sistema operacional para executar o aplicativo SAP.  

> [!TIP]
> Sempre teste a funcionalidade de failover do cluster, depois que o primeiro sistema for implantado, antes de adicionar os SIDs SAP adicionais ao cluster. Dessa forma, você saberá que a funcionalidade do cluster funciona, antes de adicionar a complexidade de sistemas SAP adicionais ao cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Implantar sistemas SAP adicionais no cluster

Neste exemplo, supomos que o System **NW1** já foi implantado no cluster. Mostraremos como implantar no cluster sistemas SAP **NW2** e **NW3**. 

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

### <a name="prerequisites"></a>Pré-requisitos 

> [!IMPORTANT]
> Antes de seguir as instruções para implantar sistemas SAP adicionais no cluster, siga as instruções para implantar o primeiro sistema SAP no cluster, pois há etapas que são necessárias apenas durante a primeira implantação do sistema.  

Esta documentação pressupõe que:
* O cluster pacemaker já está configurado e em execução.  
* Pelo menos um sistema SAP (instância de ASCS/ERS) já está implantado e está em execução no cluster.  
* A funcionalidade de failover de cluster foi testada.  
* Os compartilhamentos NFS para todos os sistemas SAP são implantados.  

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. Adicione a configuração para o sistema implantado recentemente (ou seja, **NW2**, **NW3**) ao Azure Load Balancer existente, seguindo as instruções [implantar Azure Load Balancer manualmente por meio de portal do Azure](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Ajuste os endereços IP, as portas de investigação de integridade e as regras de balanceamento de carga para sua configuração.  

2. **[A]** configuração da resolução de nomes para os sistemas SAP adicionais. Você pode usar o servidor DNS ou modificar `/etc/hosts` em todos os nós. Este exemplo mostra como usar o `/etc/hosts` arquivo.  Adapte os endereços IP e os nomes de host ao seu ambiente. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** crie os diretórios compartilhados para os sistemas SAP **NW2** e **NW3** adicionais que você está implantando no cluster. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** adicione as entradas de montagem para os sistemas de arquivos/Sapmnt/Sid e/usr/SAP/Sid/sys para os sistemas SAP adicionais que você está implantando no cluster. Neste exemplo, **NW2** e **NW3**.  

   Atualize `/etc/fstab` o arquivo com os sistemas de arquivos para os sistemas SAP adicionais que você está implantando no cluster.  

   * Se estiver usando Azure NetApp Files, siga as instruções [aqui](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)  
   * Se estiver usando o cluster GlusterFS, siga as instruções [aqui](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Instalar o ASCS/ERS

1. Crie os recursos de cluster de investigação de integridade e IP virtual para as instâncias de ASCS dos sistemas SAP adicionais que você está implantando no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ASCS, usando NFS em volumes Azure NetApp files com o protocolo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não importa em qual nó os recursos estão sendo executados.  

2. **[1]** Instalar o ASCS do SAP NetWeaver  

   Instale o ASCS do SAP NetWeaver como raiz, usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS. Por exemplo, para System **NW2**, o nome de host virtual é <b>msnw2ascs</b>, <b>10.3.1.52</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>10</b>. Para System **NW3**, o nome de host virtual é <b>msnw3ascs</b>, <b>10.3.1.54</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo, <b>20</b>. Anote em qual nó de cluster você instalou o ASCS para cada SID do SAP.  

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome do host virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Se a instalação falhar ao criar uma subpasta no/usr/SAP/**Sid**/ASCS **Instance #**, tente definir o proprietário como **Sid** ADM e o grupo como SAPs da instância de ASCS **#** e tente novamente.

3. **[1]** crie um IP virtual e recursos de cluster de investigação de integridade para a instância ers do sistema SAP adicional que você está implantando no cluster. O exemplo mostrado aqui é para **NW2** e **NW3** ers, usando NFS em volumes Azure NetApp files com o protocolo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados.  

   Em seguida, verifique se os recursos do grupo ERS recém-criado estão em execução no nó do cluster, oposto ao nó do cluster em que a instância ASCS do mesmo sistema SAP foi instalada.  Por exemplo, se NW2 ASCS foi instalado em `rhelmsscl1` , verifique se o grupo NW2 ers está em execução `rhelmsscl2` .  Você pode migrar o grupo NW2 ERS para `rhelmsscl2` executando o seguinte comando para um dos recursos de cluster no grupo: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Instalar o ERS do SAP NetWeaver

   Instale o ERS do SAP NetWeaver como raiz no outro nó, usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ERS. Por exemplo, para System **NW2**, o nome de host virtual será <b>msnw2ers</b>, <b>10.3.1.53</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>12</b>. Para System **NW3**, o nome de host virtual <b>msnw3ers</b>, <b>10.3.1.55</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>22</b>. 

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome do host virtual.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Use SWPM SP 20 PL 05 ou superior. Versões anteriores não configurarão as permissões corretamente e a instalação falhará.

   Se a instalação falhar ao criar uma subpasta no/usr/SAP/**NW2**/ers **Instance #**, tente definir o proprietário como **Sid** ADM e o grupo como SAPs da pasta ers **# #** e tente novamente.

   Se fosse necessário migrar o grupo ERS do sistema SAP implantado recentemente para um nó de cluster diferente, não se esqueça de remover a restrição de local do grupo ERS. Você pode remover a restrição executando o comando a seguir (o exemplo é fornecido para os sistemas SAP **NW2** e **NW3**). Certifique-se de remover as restrições temporárias para o mesmo recurso usado no comando para mover o grupo de clusters ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** adaptar os perfis de instância ASCS/SCS e ers para os sistemas SAP recentemente instalados. O exemplo mostrado abaixo é para NW2. Você precisará adaptar os perfis ASCS/SCS e ERS para todas as instâncias SAP adicionadas ao cluster.  
 
   * Perfil do ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     Para ENSA1 e ENSA2, verifique se os parâmetros do `keepalive` sistema operacional estão definidos conforme descrito na observação do SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Perfil do ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Atualize o arquivo / usr / sap / sapservices

   Para evitar o início das instâncias pelo script de inicialização sapinit, todas as instâncias gerenciadas pelo pacemaker devem ser comentadas do `/usr/sap/sapservices` arquivo.  O exemplo mostrado abaixo é para sistemas SAP **NW2** e **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** crie os recursos de cluster do SAP para o sistema SAP recentemente instalado.  

   Se estiver usando a arquitetura do enqueue Server 1 (ENSA1), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte maneira:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   O SAP introduziu o suporte para o servidor de enfileiramento 2, incluindo a replicação, desde o SAP NW 7.52. Da Plataforma ABAP 1809 em diante, o servidor de enfileiramento 2 é instalado por padrão. Confira a nota [2630416](https://launchpad.support.sap.com/#/notes/2630416) do SAP sobre o suporte ao servidor de enfileiramento 2.
   Se estiver usando a arquitetura do enqueue Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), defina os recursos para os sistemas SAP **NW2** e **NW3** da seguinte maneira:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, confira a observação SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Os tempos limite na configuração acima são apenas exemplos e talvez precisem ser adaptados para a configuração específica do SAP. 

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.
   O exemplo a seguir mostra o status de recursos de cluster, depois que os sistemas SAP **NW2** e **NW3** foram adicionados ao cluster. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** adicionar regras de firewall para ASCS e ers em ambos os nós.  O exemplo a seguir mostra as regras de firewall para os sistemas SAP **NW2** e **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Continuar com a instalação do SAP 

Conclua a instalação do SAP:

* [Preparando seus servidores de aplicativos SAP NetWeaver](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalando uma instância do DBMS](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Instalando um servidor de aplicativos SAP primário](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Instalando uma ou mais instâncias adicionais do aplicativo SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testar a configuração de cluster de vários SIDs

Os testes a seguir são um subconjunto dos casos de teste nos guias de práticas recomendadas do Red Hat. Eles estão incluídos para sua conveniência. Para obter a lista completa de testes de cluster, consulte a seguinte documentação:

* Se estiver usando Azure NetApp Files volumes de NFS, siga [a alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL com Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md)
* Se estiver usando altamente disponível `GlusterFS` , siga a [alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL para aplicativos SAP](./high-availability-guide-rhel.md).  

Sempre Leia os guias de práticas recomendadas do Red Hat e execute todos os testes adicionais que podem ter sido adicionados.  
Os testes apresentados estão em um cluster de dois nós, vários SID com três sistemas SAP instalados.  

1. Migre manualmente a instância de ASCS. O exemplo mostra a migração da instância de ASCS para o sistema SAP NW3.

   Estado do recurso antes de iniciar o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Execute os comandos a seguir como raiz para migrar a instância do NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Estado do recurso após o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simular falhas de nó

   Estado do recurso antes de iniciar o teste:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Execute o seguinte comando como raiz em um nó, em que pelo menos uma instância de ASCS está em execução. Neste exemplo, executamos o comando em `rhelmsscl1` , em que as instâncias de ASCS para NW1, NW2 e NW3 estão em execução.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   O status após o teste e, depois do nó, que foi iniciado novamente, deve ter a seguinte aparência.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Se houver mensagens para recursos com falha, limpe o status dos recursos com falha. Por exemplo:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]