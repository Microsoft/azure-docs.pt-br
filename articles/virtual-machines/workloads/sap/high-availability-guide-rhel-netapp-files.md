---
title: Máquinas virtuais alta disponibilidade do Azure para SAP NetWeaver no Red Hat Enterprise Linux com arquivos do Azure NetApp | Microsoft Docs
description: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503572"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Máquinas virtuais alta disponibilidade do Azure para SAP NetWeaver no Red Hat Enterprise Linux com arquivos do Azure NetApp para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível, usando [os arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Nas configurações de exemplo, comandos de instalação etc. Instância do ASCS é o número 00, a instância do ERS é número de 01, a instância do aplicativo principal (PAS) é 02 e 03 de é a instância do aplicativo (AAS). QAS de ID do sistema SAP é usado. 

A camada de banco de dados não é abordada em detalhes neste artigo.  

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação do Azure de arquivos NetApp][anf-azure-doc] 
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
* [Máquinas virtuais de planejamento e implementação para SAP no Linux do Azure][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SAP Netweaver no cluster do pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurando o ASCS/ERS do SAP Netweaver com recursos autônomos no RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurar o ASCS/ERS do SAP 4hana/s com 2 (ENSA2) de servidor de enfileiramento de autônomo no Pacemaker no RHEL ](https://access.redhat.com/articles/3974941)
* Documentação específica do RHEL do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicativos do NetApp SAP no Microsoft Azure usando arquivos do Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Visão Geral

Alta availability(HA) para serviços centrais do SAP Netweaver requer armazenamento compartilhado.
Para fazer isso no Red Hat Linux até agora era necessário criar o cluster separado de GlusterFS altamente disponível. 

Agora é possível alcançar alta disponibilidade SAP Netweaver usando o armazenamento compartilhado, implantado no Azure NetApp arquivos. Usando arquivos do Azure NetApp para o armazenamento compartilhado elimina a necessidade de adicionais [GlusterFS cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker ainda é necessária para alta disponibilidade do services(ASCS/SCS) de central SAP Netweaver.

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

O SAP NetWeaver ASCS, o SAP NetWeaver SCS, o SAP NetWeaver ERS e o banco de dados SAP HANA usam o nome do host virtual e os endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga com o IPs de front separado para (A) SCS e ERS.

> [!IMPORTANT]
> Clustering de multi-SID do SAP ASCS/ERS com Red Hat Linux como sistema operacional convidado em VMs do Azure está **não tem suporte**. Clustering de vários SID descreve a instalação de várias instâncias do SAP ASCS/ERS com SIDs diferentes em um cluster do Pacemaker.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 192.168.14.9
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS
* Porta de Investigação
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
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
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS
* Porta de Investigação
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de arquivos do Azure NetApp 

O SAP NetWeaver requer um armazenamento compartilhado para o diretório de perfil e transporte.  Antes de prosseguir com a instalação para infraestrutura de arquivos do Azure NetApp, familiarize-se com o [documentação do Azure NetApp arquivos][anf-azure-doc]. Verifique se sua região do Azure selecionada oferece NetApp serviço arquivos do Azure. O link a seguir mostra a disponibilidade dos arquivos do Azure NetApp por região do Azure: [Disponibilidade de arquivos NetApp do Azure por região do Azure][anf-avail-matrix].

Os arquivos NetApp do Azure está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Antes de implantar os arquivos do Azure NetApp, solicitar integração aos arquivos do NetApp do Azure, seguindo a [Registre-se para obter instruções de arquivos do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos de arquivos do Azure NetApp  

As etapas pressupõem que você já implantou [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos de arquivos do Azure NetApp e as VMs, em que os recursos do Azure NetApp arquivos serão montados devem ser implantadas na mesma rede Virtual do Azure ou em redes virtuais emparelhadas do Azure.  

1. Se você ainda não tiver feito isso, solicite [integração aos arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Criar a conta do NetApp na região do Azure selecionada, seguindo a [instruções para criar a conta do NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurar o pool de capacidade de arquivos do Azure NetApp, seguindo a [obter instruções sobre como configurar o pool de capacidade de arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Arquitetura do SAP Netweaver apresentada neste artigo usa um único pool de capacidade de arquivos do Azure NetApp, SKU Premium. É recomendável SKU Premium do Azure NetApp arquivos para carga de trabalho de aplicativo SAP Netweaver no Azure.  
4. Delegar uma sub-rede para os arquivos do Azure NetApp, conforme descrito na [instruções delegar uma sub-rede para arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implantar os volumes de arquivos do Azure NetApp, seguindo a [instruções para criar um volume de arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implantar os volumes nos arquivos do NetApp designado do Azure [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Tenha em mente que os recursos de arquivos do Azure NetApp e as VMs do Azure devem estar na mesma rede Virtual do Azure ou em redes virtuais emparelhadas do Azure. Neste exemplo, usamos dois volumes de arquivos do Azure NetApp: sap<b>QAS</b> e transSAP. Os caminhos de arquivo que são montados para os pontos de montagem correspondente são /usrsap<b>qas</b>sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, etc.  

   1. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. sap de volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Neste exemplo, usamos o Azure NetApp arquivos para todos os sistemas de arquivo do SAP Netweaver para demonstrar como os arquivos do Azure NetApp pode ser usados. Os sistemas de arquivos do SAP que não precisam ser montados por meio de NFS também podem ser implantados como [armazenamento de disco do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo <b>um eletrônico</b> deve estar em arquivos do Azure NetApp e <b>f-g</b> (ou seja, /usr/SAP/<b>QAS</b>/D<b>02</b>, /usr/SAP/<b>QAS </b>/D<b>03</b>) pode ser implantado como armazenamento em disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar os arquivos NetApp do Azure para o SAP Netweaver em arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é de 4 TiB. O tamanho do pool de capacidade deve ser em múltiplos de 4 TiB.
- O volume mínimo é de 100 GiB
- Os arquivos do Azure do NetApp e todas as máquinas virtuais, em que os volumes de arquivos do NetApp do Azure serão montados, deve ser na mesma rede Virtual do Azure ou no [emparelhadas redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região. Acesso de arquivos NetApp do Azure pela rede virtual emparelhamento na mesma região é suportado agora. Ainda não há suporte para acesso do Azure do NetApp por meio do emparelhamento global.
- A rede virtual selecionada deve ter uma sub-rede, delegada aos arquivos do Azure NetApp.
- Atualmente o NetApp os arquivos do Azure dá suporte a apenas NFS V3 
- Arquivos do Azure do NetApp oferece [Exportar diretiva](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar como os clientes permitidos, o tipo de acesso (ler e gravar, somente leitura, etc.). 
- O recurso de arquivos NetApp do Azure ainda não está ciente da zona. Atualmente, o recurso de arquivos do Azure NetApp não será implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja ciente das implicações de latência potenciais em algumas regiões do Azure. 

## <a name="setting-up-ascs"></a>Configuração do (A)SCS

Neste exemplo, os recursos foram implantados manualmente por meio de [portal do Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar os volumes de arquivos do Azure NetApp. Implante as VMs. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais nos pools de back-end.

1. Crie um Balanceador de Carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo **front-end. QAS. ASCS**)
         1. Defina a atribuição ao estático e digite o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para ERS do ASCS
         * Repita as etapas acima em "a" para criar um endereço IP para ERS (por exemplo **192.168.14.10** e **front-end. QAS. ERS**)
   1. Criar os pools de back-end
      1. Crie um pool de back-end para o ASCS
         1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo **back-end. QAS**)
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione o conjunto de disponibilidade criado anteriormente para ASCS 
         1. Selecione as máquinas virtuais do cluster (A)SCS
         1. Clique em OK
   1. Crie as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo **integridade. QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620**00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621**01** para ERS do ASCS
            * Repita as etapas acima em "c" para criar uma investigação de integridade para ERS (por exemplo, 621**01** e **integridade. QAS. ERS**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o balanceador de carga, selecione as regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra de Balanceador de carga (por exemplo **balanceamento de carga. QAS. ASCS.3200**)
         1. Selecione o endereço IP de front-end para o ASCS, pool de back-end e investigação de integridade criados anteriormente (por exemplo **front-end. QAS. ASCS**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Habilite o IP Flutuante**
         1. Clique em OK
      1. Portas adicionais para ASCS
         * Repita as etapas acima em "d" para as portas 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para ASCS
      1. Portas adicionais para ERS do ASCS
         * Repita as etapas acima em "d" para portas 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para o ERS do ASCS


> [!IMPORTANT]
> Não habilite os carimbos de hora do TCP em VMs do Azure colocadas por trás do balanceador de carga do Azure. Habilitar TCP carimbos de hora fará com que as investigações de integridade falha. Defina o parâmetro **net.ipv4.tcp_timestamps** à **0**. Para obter detalhes, consulte [investigações de integridade do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

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

1. **[1]**  Diretórios criar SAP no volume de arquivos do Azure NetApp.  
   Montar o volume de arquivos do Azure NetApp de temporariamente em uma das VMs e criar os diretórios do SAP (caminhos de arquivo).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
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

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +, / sapmnt/QAS sudo chattr + eu /usr/sap/trans sudo chattr + eu /usr/sap/QAS/SYS sudo chattr + eu /usr/sap/QAS/ASCS00 sudo chattr + eu /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Carregar plug-ins: langpacks, id de produto, repositórios de pesquisa-desabilitado
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata é por duas semanas. Instalar o cron yum? Ou execute: yum makecache rápida
   # <a name="installed-packages"></a>Pacotes instalados
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Versão: 3.9.5
   # <a name="release------124el7"></a>Versão: 124.el7
   # <a name="size---------100-k"></a>Tamanho: 100 k
   # <a name="repo---------installed"></a>Repositório: instalado
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>From repo   : rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Resumo: Agentes de recurso de cluster do SAP e o script de conector
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL         : https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>License     : GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Descrição: Os agentes de recurso SAP e a interface de script do conector com
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Pacemaker para permitir que as instâncias SAP a ser gerenciado em um cluster
   #          <a name="-environment"></a>: ambiente.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Adicione as seguintes linhas ao /etc/fstab, salvar e sair
    Guia de rsize difícil, da QAS nfs 192.168.24.5:/sapQAS/sapmntQAS/sapmnt/rw, = 65536, wsize = 65536, vers=2.1 = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rsize difícil, rw, = 65536, wsize = 65536, vers=2.1 = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rsize difícil, rw, = 65536, wsize = 65536, vers=2.1 = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Defina a propriedade ResourceDisk.EnableSwap como y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Criar e usar o arquivo de permuta no disco de recursos.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Defina o tamanho do arquivo de PERMUTA com propriedade ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>O espaço livre de disco de recurso varia de acordo com o tamanho da máquina virtual. Certifique-se de que você não defina um valor que é muito grande. Você pode verificar o espaço de PERMUTA com o comando swapon
   # <a name="size-of-the-swapfile"></a>Tamanho do arquivo de permuta.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pcs nó em espera anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     diretório = fstype '/ usr/sap/QAS/ASCS00' = 'nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl2-standby"></a>Nó anftstsapcl2: em espera
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Lista completa de recursos:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 iniciado
   #  <a name="resource-group-g-qasascs"></a>Grupo de recursos: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl1 iniciado
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl1 iniciado
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 iniciado
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Permita o acesso ao SWPM. Essa regra não é permanente. Se você reinicializar o computador, você precisa executar o comando novamente.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo pcs nó anftstsapcl2 unstandby sudo pcs nó em espera anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl1-standby"></a>Nó anftstsapcl1: em espera
   # <a name="online--anftstsapcl2-"></a>Online: [ anftstsapcl2 ]
   #
   # <a name="full-list-of-resources"></a>Lista completa de recursos:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl2 iniciado
   #  <a name="resource-group-g-qasascs"></a>Grupo de recursos: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 iniciado
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Iniciado anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 iniciado
   #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 iniciado
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Anftstsapcl2 iniciado
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 iniciado
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Permita o acesso ao SWPM. Essa regra não é permanente. Se você reinicializar o computador, você precisa executar o comando novamente.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Altere o comando de reinicialização para um comando de início
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>Adicione o parâmetro de keep alive
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Altere o comando de reinicialização para um comando de início
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>remover a inicialização automática do perfil do ERS
   # <a name="autostart--1"></a>AutoStart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Alterar a configuração do sistema Linux
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>No nó onde você instalou o ASCS, comente a linha a seguir
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>No nó onde você instalou o ERS, comente a linha a seguir
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: $LD_LIBRARY_PATH; Exportar LD_LIBRARY_PATH; /usr/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/perfil/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   modo de manutenção de conjunto de propriedade de pcs sudo = true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --group g-QAS_AERS
      
    a colocação de restrição de pcs sudo adicionar g-QAS_AERS com pontuação de regra do g-QAS_ASCS-5.000 sudo pcs restrição local rsc_sap_QAS_ASCS00 = 2000 runs_ers_QAS eq 1 sudo pcs restrição ordem QAS_ASCS g e g QAS_AERS tipo = opcional simétrico = false
    
    propriedade de pcs sudo pcs nó anftstsapcl1 unstandby sudo definir modo de manutenção = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    modo de manutenção de conjunto de propriedade de pcs sudo = true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --group g-QAS_AERS
      
    a colocação de restrição de pcs sudo adicionar g-QAS_AERS com g-QAS_ASCS-5.000 sudo pcs restrição ordem QAS_ASCS g e g QAS_AERS tipo = opcional simétrico = false
   
    propriedade de pcs sudo pcs nó anftstsapcl1 unstandby sudo definir modo de manutenção = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pcs status
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Lista completa de recursos:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl2 iniciado
    #  <a name="resource-group-g-qasascs"></a>Grupo de recursos: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 iniciado
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl2 iniciado
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 iniciado
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
    #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Anftstsapcl1 iniciado
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Anftstsapcl1 iniciado
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 iniciado
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 iniciado
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Porta de investigação do ASCS
   firewall sudo-cmd - zona = público – adicionar-port = 62000/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = firewall 62000/tcp sudo-cmd - zona = público – adicionar-port = 3200/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = sudo 3200/tcp firewall-cmd - zona = público – adicionar-port = 3600/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = 3600/tcp sudo firewall-cmd - zona = público – adicionar-port = 3900/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = sudo 3900/tcp firewall-cmd - zona = público – adicionar-port = 8100/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = firewall 8100/tcp sudo-cmd - zona = público – adicionar-port = 50013/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = sudo 50013/tcp firewall-cmd - zona = público – adicionar-port = 50014/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = firewall 50014/tcp sudo-cmd - zona = público – adicionar-port = 50016/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = 50016/tcp
   # <a name="probe-port-of-ers"></a>Porta de investigação do ERS
   firewall sudo-cmd - zona = público – adicionar-port = 62101/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = firewall 62101/tcp sudo-cmd - zona = público – adicionar-port = 3301/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = sudo 3301/tcp firewall-cmd - zona = público – adicionar-port = 50113/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = firewall 50113/tcp sudo-cmd - zona = público – adicionar-port = 50114/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = 50114/tcp sudo firewall-cmd - zona = público – adicionar-port = 50116/tcp-- firewall permanente sudo-cmd - zona = público – adicionar-port = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>Endereço IP da configuração de front-end do balanceador de carga para o ASCS do SAP NetWeaver
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>Endereço IP da configuração de front-end do balanceador de carga para SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr +, / sapmnt/QAS sudo chattr + eu /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Adicione as seguintes linhas ao /etc/fstab, salvar e sair
   Guia de rsize difícil, da QAS nfs 192.168.24.5:/sapQAS/sapmntQAS/sapmnt/rw, = 65536, wsize = 65536, vers=2.1 = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rsize difícil, rw, = 65536, wsize = 65536, vers=2.1 = 3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Adicione a seguinte linha para /etc/fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw, disco rígido, rsize = 65536, wsize = 65536, vers=2.1 = 3
   
   # <a name="mount"></a>Montar
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Adicione a seguinte linha para /etc/fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw, disco rígido, rsize = 65536, wsize = 65536, vers=2.1 = 3
   
   # <a name="mount"></a>Montar
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Defina a propriedade ResourceDisk.EnableSwap como y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Criar e usar o arquivo de permuta no disco de recursos.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Defina o tamanho do arquivo de PERMUTA com propriedade ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>O espaço livre de disco de recurso varia de acordo com o tamanho da máquina virtual. Certifique-se de que você não defina um valor que é muito grande. Você pode verificar o espaço de PERMUTA com o comando swapon
   # <a name="size-of-the-swapfile"></a>Tamanho do arquivo de permuta.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   Lista de hdbuserstore
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   CHAVE PADRÃO ENV: USUÁRIO 192.168.14.4:30313: SAPABAP1 BANCO DE DADOS: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   Su - hdbuserstore qasadm SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 de movimentação de recursos de pcs #
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Remova as ações com falha para o ERS que ocorreram como parte da migração
   [root@anftstsapcl1 ~] # rsc_sap_QAS_ERS01 de limpeza de recursos de pcs
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 iniciado
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 iniciado
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] eco # b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Lista completa de recursos:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   
   Ações com falha:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] # rsc_sap_QAS_ERS01 de limpeza de recursos de pcs
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] # pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] # rsc_sap_QAS_ASCS00 de limpeza de recursos de pcs [root@anftstsapcl1 ~] # rsc_sap_QAS_ERS01 de limpeza de recursos de pcs
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 iniciado
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 iniciado
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] # pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # rsc_sap_QAS_ASCS00 de limpeza de recursos de pcs [root@anftstsapcl2 ~] # rsc_sap_QAS_ERS01 de limpeza de recursos de pcs
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] # pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # rsc_sap_QAS_ERS01 de limpeza de recursos de pcs
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~] # pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Iniciado o grupo de recursos anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Introdução anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Iniciado anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 iniciado
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
