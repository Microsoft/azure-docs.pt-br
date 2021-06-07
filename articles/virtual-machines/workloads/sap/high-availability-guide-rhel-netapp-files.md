---
title: Alta disponibilidade de VMs do Azure para SAP NW no RHEL com o Azure NetApp Files | Microsoft Docs
description: Estabeleça alta disponibilidade de VMs (máquinas virtuais) do Azure para SAP NW no RHEL com o Azure NetApp Files.
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: b202613e6f24a5cd549267a1c8928e8e68caa232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303586"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux com o Azure NetApp Files para aplicativos SAP

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

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 com alta disponibilidade usando o [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
Nas configurações de exemplo, comandos de instalação etc., a instância do ASCS é o número 00, a instância do ERS é 01, a instância do Aplicativo Principal (PAS) é 02 e a instância do aplicativo (AAS) é 03. A ID do sistema SAP QAS é usada. 

A camada de banco de dados não é abordada em detalhes neste artigo.  

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure

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
  * [Configurar o SAP S/4HANA ASCS/ERS com o ENSA2 (Servidor de Enfileiramento Autônomo 2) no Pacemaker no RHEL ](https://access.redhat.com/articles/3974941)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Visão geral

A HA (alta disponibilidade) para serviços centrais do SAP NetWeaver requer armazenamento compartilhado.
Para conseguir isso no Red Hat Linux até o momento, era necessário criar um GlusterFS altamente disponível separado. 

Agora é possível obter a HA do SAP NetWeaver usando o armazenamento compartilhado, implantado no Azure NetApp Files. O uso do Azure NetApp Files para o armazenamento compartilhado elimina a necessidade de um [cluster GlusterFS](./high-availability-guide-rhel-glusterfs.md) adicional. O Pacemaker ainda é necessário para a HA dos serviços centrais do SAP NetWeaver (ASCS/SCS).

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

O SAP NetWeaver ASCS, o SAP NetWeaver SCS, o SAP NetWeaver ERS e o banco de dados SAP HANA usam o nome do host virtual e os endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. É recomendável usar o [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). A lista a seguir mostra a configuração do balanceador de carga com os IPs de front-end separados para (A)SCS e ERS.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 192.168.14.9
* Porta de Investigação
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando o Standard Load Balancer, selecione **portas de HA**
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP 192.168.14.10
* Porta de Investigação
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando o Standard Load Balancer, selecione **portas de HA**
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Como configurar a infraestrutura do Azure NetApp Files 

O SAP NetWeaver requer um armazenamento compartilhado para o diretório de perfil e transporte.  Antes de prosseguir com a configuração da infraestrutura do Azure NetApp Files, familiarize-se com a [Documentação do Azure NetApp Files][anf-azure-doc]. Verifique se a região do Azure selecionada oferece o Azure NetApp Files. O seguinte link mostra a disponibilidade de Azure NetApp Files por região do Azure: [Disponibilidade do Azure NetApp Files por região do Azure][anf-avail-matrix].

O Azure NetApp Files está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Antes de implantar o Azure NetApp Files, solicite a integração ao Azure NetApp Files, seguindo as [instruções de registro para o Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos do Azure NetApp Files  

As etapas pressupõem que você já tenha implantado a [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos do Azure NetApp Files e as VMs nas quais os volumes do Azure NetApp Files serão montados precisam ser implantados na mesma Rede Virtual do Azure ou em Redes Virtuais do Azure emparelhadas.  

1. Se você ainda não fez isso, solicite [integração ao Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  
2. Crie a conta do NetApp na região do Azure selecionada, seguindo as [instruções para criação de Conta do NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Configure o pool de capacidade do Azure NetApp Files, seguindo as [instruções sobre como configurar o pool de capacidade do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
A arquitetura do SAP NetWeaver apresentada neste artigo usa apenas um pool de capacidade do Azure NetApp Files, o SKU Premium. Recomendamos o SKU Premium do Azure NetApp Files para carga de trabalho do aplicativo SAP NetWeaver no Azure.  
4. Delegue uma sub-rede para o Azure NetApp Files conforme descrito nas [instruções em Delegar uma sub-rede ao Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implante volumes do Azure NetApp Files seguindo as [instruções para criar um volume para o Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Implante os volumes na [sub-rede](/rest/api/virtualnetwork/subnets) designada do Azure NetApp Files. Os endereços IP dos volumes do Azure NetApp são atribuídos automaticamente. Lembre-se de que os recursos do Azure NetApp Files e as VMs do Azure precisam estar na mesma Rede Virtual do Azure ou em Redes Virtuais do Azure emparelhadas. Neste exemplo, usamos dois volumes do Azure NetApp Files: sap<b>QAS</b> e transSAP. Os caminhos de arquivo montados nos pontos de montagem correspondentes são /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys etc.  

   1. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Neste exemplo, usamos o Azure NetApp Files para todos os sistemas de arquivos do SAP NetWeaver para demonstrar como o Azure NetApp Files pode ser usado. Os sistemas de arquivos SAP que não precisam ser montados via NFS também podem ser implantados como [Armazenamento em Disco do Azure](../../disks-types.md#premium-ssd). Neste exemplo, <b>a-e</b> precisam estar no Azure NetApp Files e <b>f-g</b> (ou seja, /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) podem ser implantados como Armazenamento em Disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar Azure NetApp Files para o SAP Netweaver na arquitetura de alta disponibilidade SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é de 4 TiB. O tamanho do pool de capacidade pode ser aumentado em incrementos de 1 TiB.
- O volume mínimo é de 100 GiB
- O Azure NetApp Files e todas as máquinas virtuais em que os volumes do Azure NetApp Files serão montados precisam estar na mesma Rede Virtual do Azure ou em [redes virtuais emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) na mesma região. O acesso do Azure NetApp Files por Emparelhamento VNET na mesma região agora é compatível. O acesso do Azure NetApp por emparelhamento global ainda não é compatível.
- A rede virtual selecionada precisa ter uma sub-rede, delegada ao Azure NetApp Files.
- O Azure NetApp Files oferece a [política de exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): você pode controlar os clientes permitidos e o tipo de acesso (leitura e gravação, somente leitura etc.). 
- O recurso do Azure NetApp Files ainda não tem reconhecimento de zona. No momento, o recurso do Azure NetApp Files não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure. 
- Volumes do Azure NetApp Files podem ser implantados como volumes NFSv3 ou NFSv4.1. Os dois protocolos são compatíveis com a camada de aplicativo SAP (ASCS/ERS, servidores de aplicativos SAP). 

## <a name="setting-up-ascs"></a>Configuração do (A)SCS

Neste exemplo, os recursos foram implantados manualmente por meio do [portal do Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar os volumes do Azure NetApp Files. Implante as VMs. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais no pool de back-end.

1. Crie um balanceador de carga (interno, Standard):  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IP de front-end (por exemplo, **frontend.QAS.ASCS**)
         1. Defina a Atribuição para Estático e insira o endereço IP (por exemplo, **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para o ASCS ERS
         * Repita as etapas acima no item "a" para criar um endereço IP para o ERS (por exemplo, **192.168.14.10** e **frontend.QAS.ERS**)
   1. Criar o pool de back-end
      1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
      1. Digite o nome do novo pool de back-end (por exemplo, **backend.QAS**)
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione Máquina virtual. 
      1. Selecione as máquinas virtuais do cluster (A)SCS e os respectivos endereços IP.
      1. Clique em Adicionar
   1. Crie as investigações de integridade
      1. Porta 620 **00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **health.QAS.ASCS**)
         1. Selecione TCP como protocolo, porta 620 **00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621 **01** para o ERS do ASCS
            * Repita as etapas acima em "c" para criar uma investigação de integridade para ERS (por exemplo, 621 **01** e **health.QAS.ERS**)
   1. Regras de balanceamento de carga
      1. Regras de balanceamento de carga para ASCS
         1. Abra o balanceador de carga, escolha Regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **lb.QAS.ASCS**)
         1. Selecione o endereço IP de front-end para ASCS, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **frontend.QAS.ASCS**, **backend.QAS** e **health.QAS.ASCS**)
         1. Selecione **Portas de HA**
         1. **Habilite o IP Flutuante**
         1. Clique em OK
         * Repita as etapas acima para criar regras de balanceamento de carga para ERS (por exemplo, **lb.QAS.ERS**)
1. Como alternativa, se o cenário exigir o balanceador de carga básico (interno), siga estas etapas:  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IP de front-end (por exemplo, **frontend.QAS.ASCS**)
         1. Defina a Atribuição para Estático e insira o endereço IP (por exemplo, **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para o ASCS ERS
         * Repita as etapas acima no item "a" para criar um endereço IP para o ERS (por exemplo, **192.168.14.10** e **frontend.QAS.ERS**)
   1. Criar o pool de back-end
      1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
      1. Digite o nome do novo pool de back-end (por exemplo, **backend.QAS**)
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione o Conjunto de Disponibilidade criado anteriormente para o ASCS 
      1. Selecione as máquinas virtuais do cluster (A)SCS
      1. Clique em OK
   1. Crie as investigações de integridade
      1. Porta 620 **00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **health.QAS.ASCS**)
         1. Selecione TCP como protocolo, porta 620 **00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621 **01** para o ERS do ASCS
            * Repita as etapas acima em "c" para criar uma investigação de integridade para ERS (por exemplo, 621 **01** e **health.QAS.ERS**)
   1. Regras de balanceamento de carga
      1. 32 **00** TCP para ASCS
         1. Abra o balanceador de carga, escolha Regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **lb.QAS.ASCS.3200**)
         1. Selecione o endereço IP de front-end para ASCS, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **frontend.QAS.ASCS**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Habilite o IP Flutuante**
         1. Clique em OK
      1. Portas adicionais para ASCS
         * Repita as etapas acima em "d" para as portas 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 e TCP para o ASCS
      1. Portas adicionais para ERS do ASCS
         * Repita as etapas acima em "d" para as portas 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 e TCP para o ERS do ASCS

      > [!IMPORTANT]
      > Não há suporte para IP flutuante em uma configuração de IP secundário de adaptador de rede em cenários de balanceamento de carga. Para obter detalhes, confira [Limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante um segundo adaptador de rede.  

      > [!Note]
      > Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, confira [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas subjacentemente ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, veja [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Desabilite o mapeamento de ID (se estiver usando NFSv4.1)

As instruções nesta seção só serão aplicáveis se você estiver usando volumes do Azure NetApp Files com o protocolo NFSv4.1. Execute a configuração em todas as VMs em que os volumes NFSv4.1 do Azure NetApp Files serão montados.  

1. Verifique a configuração do domínio NFS. Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja, **`defaultv4iddomain.com`** e o mapeamento está definido como **ninguém**.  

    > [!IMPORTANT]
    > É preciso que você defina o domínio NFS em `/etc/idmapd.conf` na VM para corresponder à configuração de domínio padrão no Azure NetApp Files: **`defaultv4iddomain.com`** . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp que forem montados nas VMs serão exibidas como `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verifique `nfs4_disable_idmapping`. Ele deve ser definido como **Y**. Para criar a estrutura de diretório em que `nfs4_disable_idmapping` está localizado, execute o comando mount. Você não poderá criar o diretório manualmente em /sys/modules, pois o acesso é reservado para o kernel e os drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Para obter mais detalhes sobre como alterar o parâmetro `nfs4_disable_idmapping`, confira https://access.redhat.com/solutions/1749883.

### <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor (A) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

    ```
    sudo vi /etc/hosts
    ```

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Crie diretórios SAP no volume do Azure NetApp Files.  
   Monte temporariamente o volume do Azure NetApp Files em uma das VMs e crie os diretórios SAP (caminhos de arquivo).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** Criar os diretórios compartilhados

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Instale o cliente NFS e outros requisitos

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Verificar versão de resource-agents-sap

   Certifique-se de que a versão do pacote resource-agents-sap instalado é pelo menos 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Adicionar entradas de montagem

   Se estiver usando o NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se estiver usando o NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > É necessário que a versão do protocolo NFS dos volumes do Azure NetApp Files seja correspondente ao montar os volumes. Se os volumes do Azure NetApp Files forem criados como volumes NFSv3, use a configuração NFSv3 correspondente. Se os volumes de Azure NetApp Files forem criados como volumes NFSv4.1, siga as instruções para desabilitar o mapeamento de ID e use a configuração correspondente do NFSv4.1. Neste exemplo, os volumes do Azure NetApp Files foram criados como volumes NFSv3.  

   Montar os novos compartilhamentos

   ```
   sudo mount -a  
   ```

1. **[A]** Configurar arquivo de permuta

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

1. **[A]** Configuração RHEL

   Configurar RHEL, conforme descrito na nota SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalação do ASCS/ERS do SAP NetWeaver

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Instalar o ASCS do SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz no primeiro nó usando um nome do host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS, por exemplo, <b>anftstsapvh</b>, <b>192.168.14.9</b> e o número de instância usado para a investigação do balanceador de carga, por exemplo, <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não conseguir criar uma subpasta em /usr/sap/**QAS**/ASCS **00**, tente definir o proprietário e o grupo da pasta do ASCS **00** e tente novamente.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ERS

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Instalar o ERS do SAP NetWeaver  

   Instalar o SAP NetWeaver ERS como raiz no segundo nó usando um nome do host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o ERS, por exemplo, <b>anftstsapers</b>, <b>192.168.14.10</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo, <b>01</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação falhar ao criar uma subpasta em /usr/sap/**QAS**/ERS **01**, tente definir o proprietário e o grupo da pasta do ERS **01** e tente novamente.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adaptar os perfis de instância do ASCS/SCS e do ERS

   * Perfil do ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Para ENSA1 e ENSA2, verifique se os parâmetros do sistema operacional `keepalive` estão definidos conforme descrito na nota do SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Perfil do ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Configurar Keep Alive

   A comunicação entre o servidor de aplicativos do SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS, se estiver usando ENSA1, e alterar as configurações `keepalive` do sistema Linux em todos os servidores SAP para ENSA1/ENSA2. Leia a [Nota SAP 1410736][1410736] para obter mais informações.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** Atualize o arquivo / usr / sap / sapservices

   Para evitar o início das instâncias pelo script de inicialização do sapinit, todas as instâncias gerenciadas pelo Pacemaker devem ser comentadas no arquivo / usr / sap / sapservices. Não comente a instância do SAP HANA se ela for usada com o HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Criar os recursos de cluster do SAP

   Se estiver usando a ENSA1 (arquitetura de servidor de enfileiramento 1), defina os recursos da seguinte maneira:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   O SAP introduziu o suporte para o servidor de enfileiramento 2, incluindo a replicação, desde o SAP NW 7.52. Da Plataforma ABAP 1809 em diante, o servidor de enfileiramento 2 é instalado por padrão. Confira a nota [2630416](https://launchpad.support.sap.com/#/notes/2630416) do SAP sobre o suporte ao servidor de enfileiramento 2.
   Se estiver usando a arquitetura do servidor de enfileiramento 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), instale o agente de recurso resource-agents-sap-4.1.1-12.el7.x86_64 ou mais recente e defina os recursos da seguinte maneira:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, confira a nota do SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Os tempos limite na configuração acima são apenas exemplos e talvez precisem ser adaptados à configuração específica do SAP. 

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Adicione regras de firewall para ASCS e ERS em ambos os nós. Adicione as regras de firewall para ASCS e ERS em ambos os nós.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver

   Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.  

   As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores do ASCS/SCS e do HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome do host) não são necessárias.  

   Os itens a seguir são prefixados com **[A]** – aplicável tanto a PAS quanto a AAS, **[P]** – aplicável somente a PAS ou **[S]** – aplicável somente a AAS.  

1. **[A]** Configure a resolução de nomes do host. Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir:  

   ```
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Crie o diretório sapmnt. Crie o diretório sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Instale o cliente NFS e outros requisitos  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Adicionar entradas de montagem  
   Se estiver usando o NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se estiver usando o NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Montar os novos compartilhamentos

   ```
   sudo mount -a
   ```

1. **[P]** Crie e monte o diretório do PAS  
   Se estiver usando o NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se estiver usando o NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Crie e monte o diretório do AAS  
   Se estiver usando o NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se estiver usando o NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Configurar arquivo de permuta
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Você pode usar todos os bancos de dados com suporte para esta instalação. Para obter mais informações sobre como instalar o SAP HANA no Azure, confira [Alta disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o banco de dados.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP.

1. Preparar o servidor de aplicativos

   Siga as etapas no capítulo [Preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

1. Instalar o servidor de aplicativos do SAP NetWeaver

   Instale um servidor de aplicativos do SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Atualizar o repositório seguro do SAP HANA

   Atualize o repositório seguro do SAP HANA para apontar para o nome virtual do programa de instalação da replicação de sistema do SAP HANA.

   Executar o comando a seguir para listar as entradas como \<sapsid>adm

   ```
   hdbuserstore List
   ```

   Isso deve listar todas as entradas e deve ser semelhante a
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome do host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima) e o nome do banco de dados (**QAS** na saída acima)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância do ASCS

   Estado do recurso antes de iniciar o teste:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os seguintes comandos como raiz para migrar a instância do ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simular falhas de nó

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   O status depois que o nó for iniciado novamente deve ser assim.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use o seguinte comando para limpar os recursos com falha.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Encerrar o processo do servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Execute os seguintes comandos como raiz para identificar o processo de servidor de mensagens e encerrá-lo.

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   Se você apenas encerrar o servidor de mensagens uma vez, ela será reiniciado por `sapstart`. Se você encerrá-lo com frequência suficiente, o Pacemaker finalmente moverá a instância do ASCS para o outro nó. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Encerrar processo do servidor de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução para encerrar o servidor de enfileiramento.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   A instância do ASCS deve imediatamente executar failover para o outro nó. A instância do ERS também deverá executar failover depois que a instância do ASCS for iniciada. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Interromper o processo de servidor de replicação de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ERS está em execução para encerrar o processo do servidor de replicação de enfileiramento.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   Se você executar o comando apenas uma vez, o `sapstart` reiniciará o processo. Se você executá-lo com frequência suficiente, o `sapstart` não reiniciará o processo e o recurso ficará em um estado parado. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Interromper o processo sapstartsrv de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os comandos a seguir como raiz no nó onde o ASCS está em execução.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   O processo sapstartsrv sempre deve ser reiniciado pelo agente de recursos do Pacemaker como parte do monitoramento. Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Próximas etapas

* [Guia de HA para SAP NW em VMs do Azure no RHEL para aplicativos SAP multi-SID](./high-availability-guide-rhel-multi-sid.md)
* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]