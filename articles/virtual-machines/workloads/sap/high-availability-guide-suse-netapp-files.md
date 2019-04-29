---
title: Máquinas virtuais alta disponibilidade do Azure para SAP NetWeaver no SUSE Linux Enterprise Server com arquivos do Azure NetApp | Microsoft Docs
description: Guia de alta disponibilidade do SAP NetWeaver no SUSE Linux Enterprise Server com arquivos do Azure NetApp para aplicativos SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 18bbeef833e1c82999e87451d279c0d3464af509
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711112"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com arquivos do Azure NetApp para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível, usando [Azure NetApp arquivos (em visualização pública)](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/).
É em exemplos de configurações, comandos de instalação etc., a instância do ASCS número 00, o número da instância ERS 01, a instância do aplicativo principal (PAS) é 02 e a instância do aplicativo (AAS) é 03. QAS de ID do sistema SAP é usado. 

Este artigo explica como obter alta disponibilidade do aplicativo SAP NetWeaver com arquivos do Azure NetApp. A camada de banco de dados não é abordada em detalhes neste artigo.

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação do Azure de arquivos NetApp][anf-azure-doc] 
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
* [Implantação de máquinas virtuais do Azure para SAP no Linux ][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Guias de melhores práticas SUSE SAP HA][suse-ha-guide] Os guias contêm todas as informações necessárias para configurar o Netweaver HA e a Replicação de Sistema SAP HANA no local. Use esses guias como uma linha de base geral. Eles fornecem informações muito mais detalhadas.
* [Notas de versão do SP3 com a extensão de alta disponibilidade do SUSE 12][suse-ha-12sp3-relnotes]
* [Aplicativos do NetApp SAP no Microsoft Azure usando arquivos do Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Visão geral

Alta availability(HA) para serviços centrais do SAP Netweaver requer armazenamento compartilhado.
Para fazer isso no SUSE Linux até agora era necessário criar o cluster separado de NFS altamente disponível. 

Agora é possível alcançar alta disponibilidade SAP Netweaver usando o armazenamento compartilhado, implantado no Azure NetApp arquivos. Usando arquivos do Azure NetApp para o armazenamento compartilhado elimina a necessidade de adicionais [cluster NFS](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker ainda é necessária para alta disponibilidade do services(ASCS/SCS) de central SAP Netweaver.


![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

O SAP NetWeaver ASCS, o SAP NetWeaver SCS, o SAP NetWeaver ERS e o banco de dados SAP HANA usam o nome do host virtual e os endereços IP virtuais. No Azure, uma [balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) é necessária para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga (A) SCS e ERS.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 10.1.1.20
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
  * Endereço IP 10.1.1.21
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS
* Porta de Investigação
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regras de balanceamento de carga
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de arquivos do Azure NetApp 

O SAP NetWeaver requer um armazenamento compartilhado para o diretório de perfil e transporte.  Antes de prosseguir com a instalação para infraestrutura de arquivos do Azure NetApp, familiarize-se com o [documentação de arquivos do Azure NetApp][anf-azure-doc]. Verifique se sua região do Azure selecionada oferece NetApp serviço arquivos do Azure. O link a seguir mostra a disponibilidade dos arquivos do Azure NetApp por região do Azure: [Disponibilidade de arquivos NetApp do Azure por região do Azure][anf-avail-matrix].

O recurso de arquivos NetApp do Azure está em visualização pública em várias regiões do Azure. Antes de implantar os arquivos do Azure NetApp, inscreva-se para visualização de arquivos do Azure NetApp, seguindo a [Registre-se para obter instruções de arquivos do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos de arquivos do Azure NetApp  

As etapas pressupõem que você já implantou [rede Virtual do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). Tenha em mente que os recursos de arquivos do Azure NetApp e as VMs, em que os recursos do Azure NetApp arquivos serão montados devem ser implantadas na mesma rede Virtual do Azure.  

1. Se você ainda não tiver feito isso, solicitar [registrar-se na visualização do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register).  

2. Criar a conta do NetApp na região do Azure selecionada, seguindo a [instruções para criar a conta do NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurar o pool de capacidade de arquivos do Azure NetApp, seguindo a [obter instruções sobre como configurar o pool de capacidade de arquivos do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Arquitetura do SAP Netweaver apresentada neste artigo usa um único pool de capacidade de arquivos do Azure NetApp, SKU Premium. É recomendável SKU Premium do Azure NetApp arquivos para carga de trabalho de aplicativo SAP Netweaver no Azure.  

4. Delegar uma sub-rede para os arquivos do Azure NetApp, conforme descrito na [instruções delegar uma sub-rede para arquivos do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implantar os volumes de arquivos do Azure NetApp, seguindo a [instruções para criar um volume de arquivos do Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implantar os volumes nos arquivos do NetApp designado do Azure [sub-rede](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets). Tenha em mente que os recursos de arquivos do Azure NetApp e as VMs do Azure devem estar na mesma rede Virtual do Azure. Por exemplo, sapmnt<b>QAS</b>, usrsap<b>QAS</b>, etc. são os nomes de volume e sapmnt<b>qas</b>, usrsap<b>qas</b>, etc. são o filepaths para o Azure Volumes de arquivos NetApp.  

   1. volume sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. volume usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. volume usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. volume usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. trans de volume (nfs://10.1.0.4/trans)
   6. volume usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pas)
   7. volume usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
Neste exemplo, usamos o Azure NetApp arquivos para todos os sistemas de arquivo do SAP Netweaver para demonstrar como os arquivos do Azure NetApp pode ser usados. Os sistemas de arquivos do SAP que não precisam ser montados por meio de NFS também podem ser implantados como [armazenamento de disco do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo <b>um eletrônico</b> deve estar em arquivos do Azure NetApp e <b>f-g</b> (ou seja, /usr/SAP/<b>QAS</b>/D<b>02</b>, /usr/SAP/<b>QAS </b>/D<b>03</b>) pode ser implantado como armazenamento em disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar os arquivos NetApp do Azure para o SAP Netweaver em arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é de 4 TiB. O tamanho do pool de capacidade deve ser em múltiplos de 4 TiB.
- O volume mínimo é de 100 GiB
- Os arquivos do Azure do NetApp e todas as máquinas virtuais, em que os volumes de arquivos do NetApp do Azure serão montados, deve ser na mesma rede Virtual do Azure ou no [emparelhadas redes virtuais](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) na mesma região. Acesso de arquivos NetApp do Azure pela rede virtual emparelhamento na mesma região é suportado agora. Ainda não há suporte para acesso do Azure do NetApp por meio do emparelhamento global.
- A rede virtual selecionada deve ter uma sub-rede, delegada aos arquivos do Azure NetApp.
- Atualmente o NetApp os arquivos do Azure dá suporte a apenas NFS V3 
- Arquivos do Azure do NetApp oferece [Exportar diretiva](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar como os clientes permitidos, o tipo de acesso (ler e gravar, somente leitura, etc.). 
- O recurso de arquivos NetApp do Azure ainda não está ciente da zona. Atualmente, o recurso de arquivos do Azure NetApp não será implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja ciente das implicações de latência potenciais em algumas regiões do Azure. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Implantar VMs do Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar os volumes de arquivos do Azure NetApp. Implante as VMs. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais nos pools de back-end.

1. Criar um grupo de recursos
1. Criar uma rede virtual
1. Criar um conjunto de disponibilidade para ASCS  
   Defina o máximo de domínio de atualização
1. Crie a Máquina Virtual 1  
   Use pelo menos SLES4SAP 12 SP3, neste exemplo, a imagem SLES4SAP 12 SP3 é usada  
   Selecione o conjunto de disponibilidade criado anteriormente para ASCS  
1. Crie a Máquina Virtual 2  
   Use pelo menos SLES4SAP 12 SP3, neste exemplo, a imagem SLES4SAP 12 SP3 é usada  
   Selecione o conjunto de disponibilidade criado anteriormente para ASCS  
1. Criar um conjunto de disponibilidade para as instâncias do aplicativo SAP (PAS, AAS)    
   Defina o máximo de domínio de atualização
1. Criar a máquina Virtual 3  
   Use pelo menos SLES4SAP 12 SP3, neste exemplo, a imagem SLES4SAP 12 SP3 é usada  
   Selecione o conjunto de disponibilidade criado anteriormente para PAS/AAS   
1. Criar a máquina Virtual 4  
   Use pelo menos SLES4SAP 12 SP3, neste exemplo, a imagem SLES4SAP 12 SP3 é usada  
   Selecione o conjunto de disponibilidade criado anteriormente para PAS/AAS  

## <a name="setting-up-ascs"></a>Configuração do (A)SCS

Neste exemplo, os recursos foram implantados manualmente por meio de [portal do Azure](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Implantar o balanceador de carga do Azure manualmente por meio do portal do Azure

Primeiro, você precisa criar os volumes de arquivos do Azure NetApp. Implante as VMs. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais nos pools de back-end.

1. Crie um Balanceador de Carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.1.1.20 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo **front-end. QAS. ASCS**)
         1. Defina a atribuição ao estático e digite o endereço IP (por exemplo **10.1.1.20**)
         1. Clique em OK
      1. Endereço IP 10.1.1.21 para ERS do ASCS
         * Repita as etapas acima em "a" para criar um endereço IP para ERS (por exemplo **10.1.1.21** e **front-end. QAS. ERS**)
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
         * Repita as etapas acima em "d" para portas 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para ASCS ERS

> [!IMPORTANT]
> Não habilite os carimbos de hora do TCP em VMs do Azure colocadas por trás do balanceador de carga do Azure. Habilitar TCP carimbos de hora fará com que as investigações de integridade falha. Defina o parâmetro **net.ipv4.tcp_timestamps** à **0**. Para obter detalhes, consulte [investigações de integridade do balanceador de carga](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor (A)SCS.

### <a name="installation"></a>Instalação

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Instalar o conector SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Não use traços nos nomes de host dos nós do cluster. Caso contrário, seu cluster não funcionará. Essa é uma limitação conhecida, e a SUSE está trabalhando em uma correção. A correção será lançada como um patch do pacote do conector sap-suse-cloud-connector.

   Certifique-se de que ter instalado a nova versão do conector de cluster SAP SUSE. O antigo era nomeado sap_suse_cluster_connector e o novo é nomeado **sap-suse-cluster-conector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** Atualizar agentes de recurso SAP  
   
   Um patch para o pacote de agentes de recurso é necessário para usar a nova configuração, que é descrita neste artigo. Você pode verificar, se o patch já está instalado com o comando a seguir

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A saída deverá ser semelhante a

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando grep não localizar o parâmetro IS_ERS, você precisará instalar o patch listado na [página de download do SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. **[A]** Criar os diretórios compartilhados

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Configurar o autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Crie um arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Atualmente o NetApp serviço arquivos do Azure dá suporte apenas NFS V3. Não o omita o nfsvers = opção 3.
   
   Reiniciar o autofs para montar os novos compartilhamentos
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** Configurar arquivo de permuta

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalação do ASCS/ERS do SAP NetWeaver

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ASCS

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Instalar o ASCS do SAP NetWeaver  

   Instalar o ASCS do SAP NetWeaver como raiz no primeiro nó usando um nome de host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o ASCS, por exemplo <b>anftstsapvh</b>, <b>10.1.1.20</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome de host virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Se a instalação falhará ao criar uma subpasta em /usr/SAP/**QAS**/ASCS**00**, tente definir o proprietário e o grupo do ASCS**00** pasta e tente novamente. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ERS

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Instalar o ERS do SAP NetWeaver

   Instalar o ERS do SAP NetWeaver como raiz no segundo nó usando um nome de host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o ERS, por exemplo <b>anftstsapers</b>, <b>10.1.1.21</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo <b>01</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst. Você pode usar o parâmetro SAPINST_USE_HOSTNAME para instalar o SAP, usando o nome de host virtual.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Use SWPM SP 20 PL 05 ou superior. Versões anteriores não configurarão as permissões corretamente e a instalação falhará.

   Se a instalação falhará ao criar uma subpasta em /usr/SAP/**QAS**/ERS**01**, tente definir o proprietário e o grupo do ERS**01** pasta e tente novamente.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** Adaptar os perfis de instância do ASCS/SCS e do ERS
 
   * Perfil do ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil do ERS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** Configurar Keep Alive

   A comunicação entre o servidor de aplicativos do SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as configurações do sistema Linux. Leia a [Nota SAP 1410736][1410736] para obter mais informações.

   O parâmetro enque/encni/set_so_keepalive do perfil do ASCS/SCS já foi adicionado na última etapa.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** Configurar os usuários do SAP após a instalação

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Adicionar os serviços de ERS SAP ASCS ao arquivo sapservice

   Adicione a entrada de serviço do ASCS ao segundo nó e copie a entrada de serviço de ERS para o primeiro nó.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** Criar os recursos de cluster do SAP

Se usando uma arquitetura de servidor 1 enqueue (ENSA1), defina os recursos da seguinte maneira:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Enfileirar servidor 2, incluindo a replicação, a partir do SAP NW 7.52 introduziu o suporte do SAP. Começando com o ABAP plataforma 1809, enfileirar o servidor 2 é instalado por padrão. Consulte SAP Observação [2630416](https://launchpad.support.sap.com/#/notes/2630416) para enfileirar o suporte do servidor 2.
Se usando uma arquitetura de servidor 2 enqueue ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), defina os recursos da seguinte maneira:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Se você estiver atualizando de uma versão mais antiga e alternar para o servidor de enfileiramento 2, consulte a nota sap [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver 

Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.

As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores do ASCS/SCS e do HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome do host) não são necessárias.

Os itens a seguir são prefixados com **[A]** – aplicável a PAS e AAS, **[P]** – aplicável somente ao PAS ou **[S]** – aplicável somente ao AAS.


1. **[A]** Configurar o sistema operacional

   Reduza o tamanho do cache sujo. Para obter mais informações, consulte [Baixo desempenho de gravação em servidores SLES 11/12 com RAM grande](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   ```bash
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Criar o diretório sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Criar o diretório do PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Criar o diretório AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Configurar o autofs no PAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Reiniciar o autofs para montar os novos compartilhamentos

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Configurar o autofs AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Reiniciar o autofs para montar os novos compartilhamentos

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Configurar arquivo de permuta

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Você pode usar todos os bancos de dados com suporte para esta instalação. Para obter mais informações sobre como instalar o SAP HANA no Azure, consulte [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure][sap-hana-ha]. Para obter uma lista de bancos de dados com suporte, consulte [Nota SAP 1928533][1928533].

* Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o banco de dados.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP.

1. **[A]**  Preparar servidor de aplicativo siga as etapas no capítulo [preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

2. **[A]**  Servidor de aplicativos de instalar o SAP NetWeaver instalar um servidor de aplicativos SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Repositório seguro do SAP HANA de atualização

   Atualize o repositório seguro do SAP HANA para apontar para o nome virtual do programa de instalação da replicação de sistema do SAP HANA.

   Executar o comando a seguir para listar as entradas
   <pre><code>
   hdbuserstore List
   </code></pre>

   Isso deve listar todas as entradas e deve ser semelhante a
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome do host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima) e o nome do banco de dados (**QAS** na saída acima)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Os testes a seguir são uma cópia de casos de teste a [práticas guias de práticas recomendadas do SUSE][suse-ha-guide]. Eles são copiados para sua conveniência. Sempre leia também os guias de melhores práticas e realize todos os testes adicionais que podem ter sido adicionados.

1. Teste HAGetFailoverConfig, HACheckConfig e HACheckFailoverConfig

   Execute os seguintes comandos como \<sapsid>adm no nó onde a instância do ASCS está sendo executada. Se os comandos falharem com FALHA: memória insuficiente, talvez isso seja causado por traços em seu nome de host. Isso é um problema conhecido e será corrigido pela SUSE no pacote sap-suse-cluster-connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Migrar manualmente a instância do ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Execute os seguintes comandos como raiz para migrar a instância do ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testar HAFailoverToNode

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Execute os seguintes comandos como \<sapsid>adm para migrar a instância do ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simular falhas de nó 

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Se você usar SBD, o Pacemaker não deverá iniciar automaticamente no nó eliminado. O status depois que o nó for iniciado novamente deve ser assim.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Use os comandos a seguir para iniciar o Pacemaker no nó eliminado, limpar as mensagens SBD e limpar os recursos com falha.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Reinicialização do teste manual da instância do ASCS

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Crie um bloqueio de enfileiramento, por exemplo, editando um usuário na transação su01. Execute os seguintes comandos como < sapsid\>adm no nó onde a instância do ASCS está em execução. Os comandos interromperão a instância do ASCS e a iniciarão novamente. Se usando uma arquitetura de servidor 1 enqueue, o bloqueio de enfileiramento deve ser perdida neste teste. Se usando uma arquitetura de servidor 2 enqueue, o enfileiramento será mantido. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A instância do ASCS agora deve ser desabilitada no Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Inicie a instância do ASCS novamente no mesmo nó.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   O bloqueio de enfileiramento de transação su01 deve ser perdido, se usando a arquitetura de replicação 1 do servidor de enfileiramento e o back-end deve ter sido redefinido. Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Encerrar o processo do servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute os seguintes comandos como raiz para identificar o processo de servidor de mensagens e encerrá-lo.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Se você apenas encerrar o servidor de mensagens uma vez, ela será reiniciado por sapstart. Se você encerrá-lo com frequência suficiente, o Pacemaker finalmente moverá a instância do ASCS para o outro nó. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Encerrar processo do servidor de enfileiramento

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução para encerrar o servidor de enfileiramento.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   A instância do ASCS deve imediatamente executar failover para o outro nó. A instância do ERS também deverá executar failover depois que a instância do ASCS for iniciada. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Interromper o processo de servidor de replicação de enfileiramento

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute o comando a seguir como raiz no nó onde a instância do ERS está em execução para encerrar o processo do servidor de replicação de enfileiramento.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Se você executar o comando apenas uma vez, o sapstart reiniciará o processo. Se você executá-lo com frequência suficiente, o sapstart não reiniciará o processo, e o recurso ficará em um estado parado. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ERS após o teste.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Interromper o processo sapstartsrv de enfileiramento

   Estado do recurso antes de iniciar o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Execute os comandos a seguir como raiz no nó onde o ASCS está em execução.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   O processo de sapstartsrv sempre deve ser reiniciado pelo agente de recurso do Pacemaker. Estado do recurso após o teste:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP 
* HANA no Azure (instâncias grandes), consulte [SAP HANA (instâncias grandes) alta disponibilidade e recuperação de desastres no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
