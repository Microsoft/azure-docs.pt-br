---
title: Alta disponibilidade do SAP HANA nas VMs do Azure no SLES | Microsoft Docs
description: Alta disponibilidade do SAP HANA nas VMs do Azure no SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: radeltch
ms.openlocfilehash: 42a4c4a41f6c8bdf9d4a8e78f634893722c8f389
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576383"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade do SAP HANA nas VMs do Azure no SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Para desenvolvimento local, é possível usar a Replicação de Sistema do HANA ou usar o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Em VMs (máquinas virtuais) do Azure, a Replicação de Sistema do HANA no Azure é atualmente a única função de alta disponibilidade com suporte. A replicação do SAP HANA consiste em um nó primário e, pelo menos, um nó secundário. As alterações nos dados do nó primário são replicadas no nó secundário de modo síncrono ou assíncrono.

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura de cluster e instalar e configurar a Replicação de Sistema do SAP HANA.
Nas configurações de exemplo, são usados os comandos de instalação, o número da instância **03** a ID do Sistema do HANA **HN1**.

Primeiro, leia os seguintes documentos e Notas SAP:

* A Nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP.
  * Informações importantes sobre capacidade para tamanhos de VM do Azure.
  * O software SAP e combinações de SO (sistema operacional) e banco de dados com suporte.
  * A versão do kernel do SAP necessária para Windows e Linux no Microsoft Azure.
* A Nota SAP [2015553] lista os pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2205917] tem configurações de SO recomendadas para SUSE Linux Enterprise Server para aplicativos SAP.
* A Nota SAP [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicativos SAP.
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* A Nota SAP [401162] contém informações sobre como evitar o "endereço já em uso" ao configurar a Replicação do Sistema HANA.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [SAP HANA certificada IaaS plataformas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* Guia de [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide].
* [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide] (este artigo).
* Guia de [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide].
* [Guias de melhores práticas do SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp]
  * Configuração de uma infraestrutura otimizada de desempenho do SAP HANA SR (SLES para Aplicativos SAP 12 SP1). O guia contém todas as informações necessárias para configurar a Replicação de Sistema do SAP HANA para desenvolvimento local. Use este guia como uma linha de base.
  * Configuração de uma infraestrutura otimizada para custo de SR do SAP HANA (SLES para aplicativos SAP 12 SP1)

## <a name="overview"></a>Visão geral

Para obter a alta disponibilidade, o SAP HANA é instalada em duas máquinas virtuais. Os dados são replicados usando a Replicação de Sistema do HANA.

![Visão geral de alta disponibilidade do SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

A configuração da Replicação de Sistema do SAP HANA usa um nome do host virtual dedicado e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga:

* Configuração de front-end: Endereço de IP 10.0.0.13 para hn1 db
* Configuração de back-end: Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do HANA System Replication
* Porta de Investigação: Porta 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implantar para Linux

O agente de recursos para HANA SAP está incluído no SUSE Linux Enterprise Server for SAP Applications.
O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implantar com um modelo

É possível usar um dos modelos de início rápido que estão no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, e assim por diante.
Para implantar o modelo, siga estas etapas:

1. Abra o [modelo de banco de dados][template-multisid-db] ou o [modelo convergido][template-converged] no portal do Azure. 
    O modelo de banco de dados cria as regras de balanceamento de carga apenas para um banco de dados. O modelo convergido também cria as regras de balanceamento de carga para uma instância ASCS/SCS e ERS (somente Linux). Se você planeja instalar um sistema baseado no SAP NetWeaver e quer instalar a instância ASCS/SCS nas mesmas máquinas, use o [modelo convergido][template-converged].

1. Insira os parâmetros s seguir:
    - **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que serão implantados.
    - **Tipo de pilha**: (este parâmetro é aplicável somente quando você usa o modelo convergido.) Selecione o tipo de pilha do SAP NetWeaver.
    - **Tipo de SO**: Selecione uma das distribuições do Linux. Para este exemplo, selecione **SLES 12**.
    - **Tipo de banco de dados**: Selecionar **HANA**.
    - **Tamanho do Sistema SAP**: Insira o número de SAPS que o novo sistema irá fornecer. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
    - **Disponibilidade do Sistema**: Selecione **HA**.
    - **Nome de usuário do administrador e senha do administrador**: um usuário é criado e pode ser usado para entrar no computador.
    - **Sub-rede nova ou existente**: determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente é usada. Se já tiver uma rede virtual conectada à rede local, selecione **Existente**.
    - **ID da Sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente é semelhante a **/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>** .

### <a name="manual-deployment"></a>Implantação manual

> [!IMPORTANT]
> Selecione o sistema operacional SAP certificado para o SAP HANA nos tipos de VM específicos que você está usando. A lista de tipos de VM certificados do SAP HANA e suas versões do sistema operacional podem ser pesquisados em [Plataformas de IaaS certificadas do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Clique nos detalhes do tipo de VM listado para obter a lista completa de versões do sistema operacional com suporte do SAP HANA para o tipo de VM específico
>  

1. Crie um grupos de recursos.
1. Crie uma rede virtual.
1. Crie um conjunto de disponibilidade.
   - Defina o máximo de domínio de atualização.
1. Crie um balanceador de carga (interno). Recomenda-se o [Standard Load Balancer](../../../load-balancer/load-balancer-overview.md).
   - Selecione a rede virtual criada na etapa 2.
1. Crie a máquina virtual 1.
   - Use uma imagem SLES4SAP na galeria do Azure que tem suporte para o SAP HANA no tipo de VM que você selecionou.
   - Selecione o conjunto de disponibilidade criado na etapa 3.
1. Crie a máquina virtual 2.
   - Use uma imagem SLES4SAP na galeria do Azure que tem suporte para o SAP HANA no tipo de VM que você selecionou.
   - Selecione o conjunto de disponibilidade criado na etapa 3. 
1. Adicione discos de dados.

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, confira [Limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.   

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, veja [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

1. Se estiver usando o Standard Load Balancer, siga estas etapas de configuração:
   1. Primeiro, crie um pool de IP de front-end:
   
      1. Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
      1. Defina a **Atribuição** para **Estático** e insira o endereço IP (por exemplo, **10.0.0.13**).
      1. Selecione **OK**.
      1. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.
   
   1. Em seguida, crie um pool de back-end:
   
      1. Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
      1. Selecione **Rede Virtual**.
      1. Selecione **Adicionar uma máquina virtual**.
      1. Selecione **Máquina virtual**.
      1. Selecione as máquinas virtuais do cluster SAP HANA e os respectivos endereços IP.
      1. Selecione **Adicionar**.
   
   1. Em seguida, crie uma investigação de integridade:
   
      1. Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
      1. Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
      1. Selecione **TCP** como o protocolo e porta 625 **03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
      1. Selecione **OK**.
   
   1. Em seguida, crie as regras de balanceamento de carga:
   
      1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
      1. Insira o nome da nova regra do balanceador de carga (por exemplo **hana-lb**).
      1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**, **hana-backend** e **hana-hp**).
      1. Selecione **Portas de HA**.
      1. Aumente o **tempo limite de ociosidade** para 30 minutos.
      1. Certifique-se de **habilitar IP Flutuante**.
      1. Selecione **OK**.

1. Como alternativa, se o seu cenário impõe o uso do balanceador de carga básico, siga estas etapas de configuração:
   1. Primeiro, crie um pool de IP de front-end:
   
      1. Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
      1. Defina a **Atribuição** para **Estático** e insira o endereço IP (por exemplo, **10.0.0.13**).
      1. Selecione **OK**.
      1. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.
   
   1. Em seguida, crie um pool de back-end:
   
      1. Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
      1. Selecione **Adicionar uma máquina virtual**.
      1. Selecione o conjunto de disponibilidade criado na etapa 3.
      1. Selecione as máquinas virtuais do cluster do SAP HANA.
      1. Selecione **OK**.
   
   1. Em seguida, crie uma investigação de integridade:
   
      1. Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
      1. Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
      1. Selecione **TCP** como o protocolo e porta 625 **03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
      1. Selecione **OK**.
   
   1. Para o SAP HANA 1.0, crie as regras de balanceamento de carga:
   
      1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
      1. Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3 **03** 15).
      1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 15.
      1. Aumente o **tempo limite de ociosidade** para 30 minutos.
      1. Certifique-se de **habilitar IP Flutuante**.
      1. Selecione **OK**.
      1. Repita essas etapas para a porta 3 **03** 17.
   
   1. Para o SAP HANA 2.0, crie as regras de balanceamento de carga para o banco de dados do sistema:
   
      1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
      1. Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3 **03** 13).
      1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 13.
      1. Aumente o **tempo limite de ociosidade** para 30 minutos.
      1. Certifique-se de **habilitar IP Flutuante**.
      1. Selecione **OK**.
      1. Repita essas etapas para a porta 3 **03** 14.
   
   1. Para o SAP HANA 2.0, primeiro crie as regras de balanceamento de carga para o banco de dados do locatário:
   
      1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
      1. Insira o nome da nova regra do balanceador de carga (por exemplo hana-lb-3 **03** 40).
      1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **hana-frontend**).
      1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 40.
      1. Aumente o **tempo limite de ociosidade** para 30 minutos.
      1. Certifique-se de **habilitar IP Flutuante**.
      1. Selecione **OK**.
      1. Repita essas etapas para portas 3 **03** 41 e 3 **03** 42.

   Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [Conexões aos bancos de dados de locatário](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no guia [Bancos de dados de locatário do SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ou [Nota SAP 2388694][2388694].

> [!IMPORTANT]
> Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas de forma subjacente em relação ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, veja [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Veja também a nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster do Pacemaker

Siga as etapas em [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster Pacemaker básico para esse servidor HANA. É possível usar o mesmo cluster do Pacemaker para SAP HANA e SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalar SAP HANA

As etapas nesta seção usam os seguintes prefixos:
- **[A]** : A etapa se aplica a todos os nós.
- **[1]** : A etapa se aplica apenas ao nó 1.
- **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[A]** Configurar o layout de disco: discos sem formatação: **Gerenciamento de volumes lógicos (LVM)** .

   É recomendável que você use o LVM para volumes que armazenam dados e arquivos de log. O exemplo a seguir assume que as máquinas virtuais tenham quatro discos de dados anexados que são usados para criar dois volumes.

   Listar todos os discos disponíveis:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Saída de exemplo:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie volumes físicos para todos os discos que você quer usar:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie um grupo de volumes para os arquivos de dados. Use um grupo de volumes para os arquivos de log e outro para o diretório compartilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Criar os volumes lógicos. Um volume linear é criado quando você usa `lvcreate` sem a opção `-i`. Sugerimos que você crie um volume distribuído para melhorar o desempenho de E/S e alinhe os tamanhos de distribuição aos valores documentados em [Configurações de armazenamento de VM do SAP HANA](./hana-vm-operations-storage.md). O argumento `-i` deve ser o número de volumes físicos subjacentes e o argumento `-I` é o tamanho da distribuição. Neste documento, dois volumes físicos são usados para o volume de dados, portanto, o argumento da chave `-i` é definido com **2**. O tamanho da distribuição para o volume de dados é de **256KiB**. Um volume físico é usado para o volume do log, portanto, nenhuma opção `-i` ou `-I` é usada explicitamente para os comandos do volume do log.  

   > [!IMPORTANT]
   > Use a opção `-i` e defina-a para o número do volume físico subjacente quando você usar mais de um volume físico para cada volume de dados, log ou compartilhado. Use a opção `-I` para especificar o tamanho da distribuição, quando criar um volume distribuído.  
   > Veja [Configurações de armazenamento de VM do SAP HANA](./hana-vm-operations-storage.md) para ver as configurações de armazenamento recomendadas, incluindo tamanhos de distribuição e número de discos.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Crie os diretórios de montagem e copie o UUID de todos os volumes lógicos:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Crie entradas `fstab` para os três volumes lógicos:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Insira a seguinte linha no arquivo `/etc/fstab`:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monte os novos volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Configurar o layout de disco: discos sem formatação: **Discos simples**.

   Para sistemas de demonstração, você pode colocar os arquivos de log e dados do HANA em um disco. Crie uma partição em /dev/disk/azure/scsi1/lun0 e formate-a com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no arquivo /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Crie o diretório de destino e monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Configure a resolução do nome do host para todos os hosts.

   É possível usar um servidor DNS ou modificar o arquivo /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas no arquivo /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Instale os pacotes de alta disponibilidade do SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Para instalar a Replicação de Sistema do SAP HANA, siga o capítulo 4 do [guia Cenário otimizado para desempenho do SR do SAP HANA](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Execute o programa **hdblcm** do DVD do HANA. Insira os valores a seguir no prompt:
   * Escolha instalação: Insira **1**.
   * Selecione os componentes adicionais para instalação: Insira **1**.
   * Insira o caminho de instalação [/hana/shared]: Selecione Enter.
   * Insira o Nome do Host Local: Selecione Enter.
   * Você deseja adicionar outros hosts ao sistema? (s/n) [n]: Selecione Enter.
   * Insira a ID do Sistema SAP HANA: Insira o SID do HANA, por exemplo: **HN1**.
   * Insira o Número da Instância [00]: Insira o número de instância do HANA. Insira **03** se você usou o modelo do Azure ou seguiu a seção de implantação manual deste artigo.
   * Selecione Modo de Banco de Dados/Inserir Índice [1]: Selecione Enter.
   * Selecione o Uso do Sistema/Inserir Índice [4]: Selecione o valor de uso do sistema.
   * Insira o Local dos Volumes de Dados [/hana/data/HN1]: Selecione Enter.
   * Insira o Local dos Volumes de Log [/hana/log/HN1]: Selecione Enter.
   * Restringir a alocação máxima de memória? [n]: Selecione Enter.
   * Insira o Nome do Host do Certificado para o '...' [...]: Selecione Enter.
   * Insira a Senha de Usuário do Agente do Host SAP (sapadm): Insira a senha de usuário do agente de host.
   * Confirme a Senha de Usuário do Agente do Host SAP (sapadm): Insira a senha do usuário do agente de host novamente para confirmar.
   * Insira a Senha do Administrador de Sistema (hdbadm): Insira a senha de administrador do sistema.
   * Confirme a Senha do Administrador de Sistema (hdbadm): Insira a senha do usuário do agente administrador novamente para confirmar.
   * Insira o Diretório Base do Administrador de Sistema [/usr/sap/HN1/home]: Selecione Enter.
   * Insira o Shell de Logon do Administrador de Sistema [/bin/sh]: Selecione Enter.
   * Insira a ID de Usuário do Administrador de Sistema [1001]: Selecione Enter.
   * Insira a ID do Grupo de Usuários (sapsys) [79]: Selecione Enter.
   * Insira a Senha de Usuário do Banco de Dados (SYSTEM): Senha de Usuário do Banco de Dados.
   * Confirme a Senha de Usuário do Banco de Dados (SYSTEM): Insira a senha do usuário novamente para confirmar.
   * Reiniciar o sistema após a reinicialização do computador? [n]: Selecione Enter.
   * Deseja continuar? (s/n): Valide o resumo. Insira **y** para continuar.

1. **[A]** Atualize o Agente de Host do SAP.

   Baixe o último arquivo do Agente de Host do SAP no [Centro de Software do SAP][sap-swcenter] e execute o comando a seguir para atualizar o agente. Substitua o caminho do arquivo para apontar para o arquivo que você baixou:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do Sistema SAP HANA 2.0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica apenas ao nó 1.
* **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[1]** Crie o banco de dados de locatário.

   Se você estiver usando o SAP HANA 2.0 ou o MDC, crie um banco de dados de locatário para o sistema SAP NetWeaver. Substitua **NW1** pelo SID do sistema SAP.

   Execute o seguinte comando como <hanasid\>adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configure a Replicação de Sistema no primeiro nó:

   Faça backup dos bancos de dados como <hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os arquivos PKI do sistema para o site secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Crie o site primário:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configure a Replicação de Sistema no segundo nó:
    
   Registre o segundo nó para iniciar a replicação de sistema. Execute o seguinte comando como <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do Sistema SAP HANA 1.0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica apenas ao nó 1.
* **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[1]** Crie os usuários necessários.

   Execute o comando a seguir como raiz. Certifique-se de substituir as cadeias em negrito (ID do Sistema HANA **HN1** e o número da instância **03**) pelos valores da sua instalação do SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Crie a entrada do repositório de chaves.

   Execute o comando a seguir para criar uma nova entrada de repositório de chaves:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Faça backup do banco de dados.

   Faça backup dos bancos de dados como raiz:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se você usar uma instalação de vários locatários, faça também backup do banco de dados do locatário:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configure a Replicação de Sistema no primeiro nó.

   Crie o site primário como <hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configure a Replicação de Sistema no nó secundário.

   Registre o site secundário como <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Crie os recursos de cluster do SAP HANA

Primeiro, crie a topologia HANA. Execute os seguintes comandos em um dos nós de cluster do Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Em seguida, crie os recursos do HANA:

> [!IMPORTANT]
> Testes recentes revelaram situações em que o netcat deixa de responder às solicitações devido à lista de pendências e à limitação dele de manipular apenas uma conexão. O recurso netcat deixa de escutar as solicitações do Azure Load Balancer e o IP flutuante fica não disponível.  
> Para os clusters existentes do Pacemaker, recomendamos a substituição do netcat pelo socat. No momento, é recomendável usar o agente de recursos do azure-lb, que faz parte do pacote resource-agents, com os seguintes requisitos de versão do pacote:
> - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos resource-agents-4.3.018.a7fb5035-3.30.1.  
> - Para o SLES 15/15 SP1, a versão deve ser pelo menos resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Observe que a alteração exigirá um breve tempo de inatividade.  
> Para clusters do Pacemaker existentes, se a configuração já foi alterada para usar socat conforme descrito em [Proteção de Detecção do Azure Load Balancer](https://www.suse.com/support/kb/doc/?id=7024128), não há nenhum requisito para mudar imediatamente para o agente de recursos azure-lb.


> [!NOTE]
> Este artigo contém referências aos termos *mestre* e *escravo*, os quais a Microsoft não usa mais. Quando esses termos forem removidos do software, nós os removeremos deste artigo.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Certifique-se de que o status do cluster está correto e que todos os recursos foram iniciados. Não é importante saber em qual nó os recursos estão sendo executados.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Configurar a replicação de sistema ativa/habilitada para leitura do HANA no cluster do Pacemaker

Começando com o SAP HANA 2.0 SPS 01, o SAP permite a configuração ativa/habilitada para leitura da replicação de sistema do SAP HANA, em que os sistemas secundários de replicação de sistema do SAP HANA podem ser usados ativamente para cargas de trabalho com uso intensivo de leitura. Para dar suporte a essa configuração em um cluster, um segundo endereço IP virtual é necessário. Isso permite que os clientes acessem o banco de dados secundário do SAP HANA habilitado para leitura. Para garantir que o site de replicação secundária ainda possa ser acessado após uma tomada de controle, o cluster precisa mover o endereço IP virtual com o secundário do recurso SAPHana.

Esta seção descreve as etapas adicionais necessárias para gerenciar a replicação de sistema ativa/habilitada para leitura do HANA em um cluster de alta disponibilidade do SUSE com um segundo IP virtual.    
Antes de continuar, verifique se você configurou totalmente o cluster de alta disponibilidade do SUSE, gerenciando o banco de dados do SAP HANA conforme descrito nos segmentos acima da documentação.  

![Alta disponibilidade do SAP HANA com secundário habilitado para leitura](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Configuração adicional no Azure Load Balancer para configuração ativa/habilitada para leitura

Para continuar com as etapas adicionais sobre o provisionamento do segundo IP virtual, verifique se você configurou o Azure Load Balancer conforme descrito na seção [Implantação manual](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment).

1. Para o **Standard** Load Balancer, siga as etapas adicionais abaixo no mesmo balanceador de carga que você criou na seção anterior.

   a. Crie um segundo pool de IPs de front-end: 

   - Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
   - Insira o nome do segundo pool de IPs de front-end (por exemplo, **hana-secondaryIP**).
   - Defina a **Atribuição** como **Estático** e insira o endereço IP (por exemplo, **10.0.0.14**).
   - Selecione **OK**.
   - Depois que o novo pool de IPs de front-end for criado, anote o endereço IP de front-endereço IP de front-end.

   b. Em seguida, crie uma investigação de integridade:

   - Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
   - Insira o nome da nova investigação de integridade (por exemplo, **hana-secondaryhp**).
   - Selecione **TCP** como o protocolo e a porta **62603**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
   - Selecione **OK**.

   c. Em seguida, crie as regras de balanceamento de carga:

   - Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
   - Insira o nome da nova regra do balanceador de carga (por exemplo **hana-secondarylb**).
   - Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-secondaryIP**, **hana-backend** e **hana-secondaryhp**).
   - Selecione **Portas de HA**.
   - Aumente o **tempo limite de ociosidade** para 30 minutos.
   - Certifique-se de **habilitar IP Flutuante**.
   - Selecione **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Configurar a replicação de sistema ativa/habilitada para leitura do HANA

As etapas para configurar a replicação do sistema HANA são descritas na seção [Configurar a replicação do sistema SAP HANA 2.0](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication). Se você estiver implantando o cenário secundário habilitado para leitura, ao configurar a replicação do sistema no segundo nó, execute o seguinte comando como **hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Como adicionar um recurso de endereço IP virtual secundário para uma configuração ativa/habilitada para leitura

O segundo IP virtual e a restrição de colocação apropriada podem ser configurados com os seguintes comandos:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Certifique-se de que o status do cluster está correto e que todos os recursos foram iniciados. O segundo IP virtual será executado no site secundário junto com o recurso secundário SAPHana.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

Na próxima seção, você pode encontrar o conjunto típico de testes de failover a serem executados.

Lembre-se do comportamento do segundo IP virtual, ao testar um cluster HANA configurado com um secundário habilitado para leitura:

1. Quando você migra o recurso de cluster **SAPHana_HN1_HDB03** para **hn1-db-1**, o segundo IP virtual será movido para o outro **servidor hn1-db-0**. Caso você tenha configurado AUTOMATED_REGISTER="false" e a replicação do sistema HANA não tenha sido registrada automaticamente, o segundo IP virtual é executado em **hn1-db-0**, pois o servidor está disponível e os serviços de cluster estão online.  

2. Ao testar uma falha do servidor, os recursos do segundo IP virtual (**rsc_secip_HN1_HDB03**) e o recurso de porta do Azure Load Balancer (**rsc_secnc_HN1_HDB03**) serão executados no servidor primário junto com os recursos de IP virtual primários. Enquanto o servidor secundário está inativo, os aplicativos que estão conectados ao banco de dados HANA habilitado para leitura vão se conectar ao banco de dados HANA primário. O comportamento é esperado porque você não deseja que os aplicativos conectados ao banco de dados HANA habilitado para leitura fiquem inacessíveis enquanto o servidor secundário não estiver disponível.
  
3. Quando o servidor secundário estiver disponível e os serviços de cluster estiverem online, o segundo IP virtual e os recursos de porta serão movidos automaticamente para o servidor secundário, mesmo que a replicação do sistema HANA não possa ser registrada como secundária. Você precisa garantir que registrou o banco de dados secundário do HANA como habilitado para leitura antes de iniciar os serviços de cluster nesse servidor. Você pode configurar o recurso de cluster da instância do HANA para registrar automaticamente o secundário definindo o parâmetro AUTOMATED_REGISTER=true.       

4. Durante o failover e o fallback, as conexões existentes para aplicativos, usando o segundo IP virtual para se conectar ao banco de dados do HANA, podem ser interrompidas.  

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como é possível testar a configuração. Todos os testes assumem que você é raiz e o mestre do SAP HANA está em execução na máquina virtual **hn1-db-0**.

### <a name="test-the-migration"></a>Testar a migração

Antes de iniciar o teste, certifique-se de que o Pacemaker não tem nenhuma ação com falha (via crm_mon -r), não há restrições de locais inesperadas (por exemplo, sobras de um teste de migração) e que o HANA está em estado de sincronização, por exemplo, com SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

É possível migrar o nó mestre do SAP HANA executando o comando a seguir:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se você definir `AUTOMATED_REGISTER="false"`, essa sequência de comandos deverá migrar o nó mestre do SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Quando a migração estiver concluída, a saída crm_mon -r será semelhante a esta

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

O recurso do SAP HANA em hn1-db-0 falhará em ser iniciado como secundário. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Também é necessário limpar o estado do recurso de nó secundário:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitore o estado do recurso do HANA usando crm_mon -r. Depois que o HANA for iniciado em hn1-db-0, a saída deverá ser semelhante a esta

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testar o agente de isolamento do Azure (não SBD)

É possível testar a configuração do agente de isolamento do Azure, desabilitando a interface de rede no nó hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

A máquina virtual agora deverá reiniciar ou parar, dependendo da configuração do cluster.
Se você definir a configuração `stonith-action` como desativada, a máquina virtual será interrompida e os recursos migrados para a máquina virtual em execução.

Após iniciar a máquina virtual novamente, o recurso SAP HANA não será iniciado como secundário se você definir `AUTOMATED_REGISTER="false"`. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testar isolamento de SBD

É possível testar a configuração de SBD eliminando o processo de inquisidor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

O nó de cluster hn1-db-0 deve ser reiniciado. O serviço Pacemaker pode não ser iniciado posteriormente. Certifique-se de iniciá-lo novamente.

### <a name="test-a-manual-failover"></a>Testar um failover manual

Você pode testar um failover manual, parando o serviço `pacemaker` no nó hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Após o failover, você pode iniciar o serviço novamente. Se você definir `AUTOMATED_REGISTER="false"`, o recurso do SAP HANA no nó hn1-db-0 não será iniciado como secundário. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testes de SUSE

> [!IMPORTANT]
> Selecione o sistema operacional SAP certificado para o SAP HANA nos tipos de VM específicos que você está usando. A lista de tipos de VM certificados do SAP HANA e suas versões do sistema operacional podem ser pesquisados em [Plataformas de IaaS certificadas do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Clique nos detalhes do tipo de VM listado para obter a lista completa de versões do sistema operacional com suporte do SAP HANA para o tipo de VM específico

Execute todos os casos de teste listados no guia Cenário otimizado para desempenho do SR do SAP HANA ou Cenário otimizado para custo do SR do SAP HANA, dependendo do caso de uso. Os guias podem ser encontrados na [página de melhores práticas do SLES para SAP][sles-for-sap-bp].

Os testes a seguir são uma cópia das descrições de teste do guia de Cenário otimizado para desempenho do SR do SAP HANA do SUSE Linux Enterprise Server para Aplicativos SAP 12 SP1. Para uma versão atualizada, sempre leia também o próprio guia. Antes de iniciar o teste, sempre certifique-se de que o HANA está em sincronização e também que a configuração do Pacemaker está correta.

Nas descrições de teste a seguir, assumimos PREFER_SITE_TAKEOVER="true" e AUTOMATED_REGISTER="false".
OBSERVAÇÃO: os testes a seguir foram projetados para serem executados em sequência e dependem do estado de saída dos testes anteriores.

1. TESTE 1: PARAR O BANCO DE DADOS PRIMÁRIO NO NÓ 1

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O Pacemaker deve detectar a instância do HANA parada e o failover para o outro nó. Depois que o failover é concluído, a instância do HANA no nó hn1-db-0 é interrompida porque o Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registrar o nó hn1-db-0 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 2: PARAR O BANCO DE DADOS PRIMÁRIO NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O Pacemaker deve detectar a instância do HANA parada e o failover para o outro nó. Depois que o failover é concluído, a instância do HANA no nó hn1-db-1 é interrompida porque o Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registrar o nó hn1-db-1 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 3: BANCO DE DADOS PRIMÁRIO COM FALHA NO NÓ

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   O Pacemaker deve detectar a instância do HANA encerrada e o failover para o outro nó. Depois que o failover é concluído, a instância do HANA no nó hn1-db-0 é interrompida porque o Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registrar o nó hn1-db-0 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 4: BANCO DE DADOS PRIMÁRIO COM FALHA NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O Pacemaker deve detectar a instância do HANA encerrada e o failover para o outro nó. Depois que o failover é concluído, a instância do HANA no nó hn1-db-1 é interrompida porque o Pacemaker não registra automaticamente o nó como secundário do HANA.

   Execute os seguintes comandos para registrar o nó hn1-db-1 como secundário e limpar o recurso com falha.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 5: NÓ DO SITE PRIMÁRIO COM FALHA (NÓ 1)

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os comandos a seguir como raiz no nó hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   O Pacemaker deve detectar o nó do cluster encerrado e isolar o nó. Depois que o nó for isolado, o Pacemaker disparará a tomada de controle da instância do HANA. Quando o nó isolado for reiniciado, o Pacemaker não iniciará automaticamente.

   Execute os comandos a seguir para iniciar o Pacemaker, limpe as mensagens do SBD para o nó hn1-db-0, registre o nó hn1-db-0 como secundário e limpe o recurso com falha.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TESTE 6: NÓ DO SITE SECUNDÁRIO COM FALHA (NÓ 2)

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Execute os comandos a seguir como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   O Pacemaker deve detectar o nó do cluster encerrado e isolar o nó. Depois que o nó for isolado, o Pacemaker disparará a tomada de controle da instância do HANA. Quando o nó isolado for reiniciado, o Pacemaker não iniciará automaticamente.

   Execute os comandos a seguir para iniciar o Pacemaker, limpe as mensagens do SBD para o nó hn1-db-1, registre o nó hn1-db-1 como secundário e limpe o recurso com falha.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 7: PARAR BANCO DE DADOS SECUNDÁRIO NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   O Pacemaker detectará a instância do HANA parada e marcará o recurso como com falha no nó hn1-db-1. O Pacemaker deverá reiniciar automaticamente a instância do HANA. Execute o seguinte comando para limpar o estado com falha.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TESTE 8: BANCO DE DADOS SECUNDÁRIO COM FALHA NO NÓ 2

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os seguintes comandos como <hanasid\>adm no nó hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   O Pacemaker detectará a instância do HANA encerrada e marcará o recurso como com falha no nó hn1-db-1. Execute o seguinte comando para limpar o estado com falha. Em seguida, o Pacemaker deverá reiniciar automaticamente a instância do HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: NÓ DO SITE SECUNDÁRIO COM FALHA (NÓ 2) EXECUTANDO BANCO DE DADOS HANA SECUNDÁRIO

   Estado do recurso antes de iniciar o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Execute os comandos a seguir como raiz no nó hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   O Pacemaker deve detectar o nó do cluster encerrado e isolar o nó. Quando o nó isolado for reiniciado, o Pacemaker não iniciará automaticamente.

   Execute os seguintes comandos para iniciar o Pacemaker, limpe as mensagens do SBD para o nó hn1-db-1 e limpe o recurso com falha.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Estado do recurso após o teste:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
