---
title: Configurações e operações de infraestrutura do SAP HANA no Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais (VMs) do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6e28b23f20a0336498abbc357f4c96bdfa5b089f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881831"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este documento fornece orientação para configurar a infraestrutura do Azure e operar sistemas SAP HANA que são implantados em máquinas virtuais nativas (VMs) do Azure. O documento também inclui informações de configuração de expansão para a SKU de VM M128s do SAP HANA. Este documento não pretende substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Observações sobre o SAP](https://service.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa ter um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Azure](../../linux/tutorial-manage-vm.md)
- [Rede do Azure e redes virtuais](../../linux/tutorial-virtual-network.md)
- [Armazenamento do Azure](../../linux/tutorial-manage-disks.md)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte a seção [SAP no Azure](./get-started.md) na [documentação do Azure](../../../index.yml).

## <a name="basic-setup-considerations"></a>Considerações básicas sobre a instalação
As seções a seguir descrevem considerações de configuração básica para implantar sistemas SAP HANA em máquinas virtuais do Azure.

### <a name="connect-into-azure-virtual-machines"></a>Conecte-se em máquinas virtuais do Azure
Conforme documentado no [guia de planejamento de máquinas virtuais do Azure](./planning-guide.md), há dois métodos básicos para se conectar em máquinas virtuais do Azure:

- Conexão pela Internet e por pontos de extremidade públicos em uma VM de atalho ou na VM que executa o SAP HANA.
- Conexão por meio de uma [VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Uma conectividade site a site por meio de VPN ou rota expressa é necessária para cenários de produção. Esse tipo de conexão também é necessário para cenários de não produção que alimentam em cenários de produção em que o software SAP está sendo usado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure que podem ser usados para cenários de produção são listados na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, você pode escolher uma variedade maior de VMs nativas do Azure.

>[!NOTE]
> Para cenários de não produção, use os tipos de VM que são listados na [nota da SAP nº 1928533](https://launchpad.support.sap.com/#/notes/1928533). Para o uso de VMs do Azure para cenários de produção, verifique as VMs certificadas para SAP HANA na [lista de plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Implante as VMs no Azure usando:

- O portal do Azure.
- Cmdlets do Azure PowerShell.
- A CLI do Azure.

Você também pode implantar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure por meio da [plataforma SAP Cloud](https://cal.sap.com/). O processo de instalação está descrito em [Implantar SAP S/4HANA ou BW/4HANA no Azure](./cal-s4h.md) ou com a automação liberada [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Para usar as VMs M208xx_v2, você precisa ter cuidado ao selecionar a imagem do Linux na Galeria de imagens de VM do Azure. Para ler os detalhes, leia o artigo [Tamanhos de máquina virtual com otimização de memória](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Configuração de armazenamento para SAP HANA
Para obter as configurações de armazenamento e os tipos de armazenamento a serem usados com SAP HANA no Azure, leia o documento [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md).


### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando você tiver conectividade site a site no Azure por meio de VPN ou ExpressRoute, você deve ter pelo menos uma rede virtual do Azure que está conectada por meio de um Gateway Virtual ao circuito de ExpressRoute ou VPN. Em implantações simples, o Gateway Virtual pode ser implantado em uma sub-rede de rede virtual do Azure (VNet) que hospeda as instâncias do SAP HANA também. Para instalar o SAP HANA, você cria duas sub-redes adicionais dentro da rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede executa o Jumpbox ou as VMs de gerenciamento para hospedar o SAP HANA Studio, outros softwares de gerenciamento ou o software do aplicativo.

> [!IMPORTANT]
> Fora de funcionalidade, mas mais importante por motivos de desempenho, não há suporte para configurar [Dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S / 4HANA baseado no sistema SAP. É necessário a comunicação entre a camada de aplicativo SAP e a camada DBMS seja direta. A restrição não inclui [Regras de NSG e ASG do Azure](../../../virtual-network/network-security-groups-overview.md), desde que as regras de NSG e ASG permitam uma comunicação direta. Outros cenários em que não há suporte para NVAs estão em caminhos de comunicação entre VMs do Azure que representam nós de cluster do Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](./high-availability-guide-suse.md). Ou em caminhos de comunicação entre as VMs do Azure e o SOFS do Windows Server, conforme descrito em [Cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](./sap-high-availability-guide-wsfc-file-share.md). Os NVAs em caminhos de comunicação podem facilmente duplicar a latência de rede entre dois parceiros de comunicação, podendo restringir o rendimento em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários observados com os clientes, os NVAs podem causar falhas nos clusters do Pacemaker Linux nos casos em que as comunicações entre os nós do cluster do Linux Pacemaker precisam se comunicar com o dispositivo SBD por meio de um NVA.  
> 

> [!IMPORTANT]
> Outro design que **NÃO** tem suporte é a segregação da camada de aplicativo SAP e da camada DBMS em diferentes redes virtuais do Azure que não estejam [emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) entre si. É recomendável separar a camada de aplicativo SAP e a camada DBMS usando sub-redes em uma rede virtual do Azure, em vez de usar redes virtuais diferentes do Azure. Se você decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em uma rede virtual diferente, as duas redes virtuais deverão ser [emparelhadas](../../../virtual-network/virtual-network-peering-overview.md). Esteja ciente de que o tráfego entre duas redes virtuais [emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) do Azure está sujeito a custos de transferência. Com o enorme volume de dados em muitos Terabytes trocados entre a camada de aplicativo SAP e a camada DBMS, custos substanciais poderão ser acumulados se a camada de aplicativo SAP e a camada DBMS forem segregadas entre duas redes virtuais do Azure. 

Quando você instala as VMs para executar o SAP HANA, as VMs necessitam de:

- Duas NICs virtuais instaladas: uma NIC para conectar-se à sub-rede de gerenciamento, e uma NIC para conectar-se da rede local ou de outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implantados para as duas NICS virtuais.

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do SO convidado para um vNIC. Alguns serviços do Azure, como o serviço de Backup do Azure, dependem do fato de que pelo menos o vNIC primário esteja definido como DHCP, e não para endereços IP estáticos. Veja também o documento [Solucionar problemas de backup da máquina virtual do Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking). Se você precisar atribuir vários endereços IP estáticos a uma VM, precisará atribuir vários vNICs a uma VM.
>
>

No entanto, para implantações que são permanentes, você precisará criar uma arquitetura de rede do datacenter virtual no Azure. Essa arquitetura recomenda a separação de Gateway de VNet do Azure que se conecta localmente em uma VNet separada do Azure. Essa VNet separada deve hospedar todo o tráfego que sai para o local ou na Internet. Essa abordagem permite que você implantar o software para auditoria e registro em log o tráfego que entra o datacenter virtual no Azure neste hub separado de rede virtual. Portanto, você tem uma VNet que hospeda todo o software e as configurações relacionadas ao tráfego de entrada e saída para sua implantação do Azure.

Os artigos [Datacenter Virtual do Azure: Uma perspectiva de rede](/azure/architecture/vdc/networking-virtual-datacenter) e [Datacenter Virtual do Azure e o Plano de Controle Empresarial](/azure/architecture/vdc/) fornecem mais informações sobre a abordagem do datacenter virtual e o design relacionado da VNet do Azure.


>[!NOTE]
>O tráfego que flui entre uma VNet do hub e spoke VNet usando [emparelhamento de rede virtual do Azure](../../../virtual-network/virtual-network-peering-overview.md) é o assunto adicionais [custos](https://azure.microsoft.com/pricing/details/virtual-network/). Com base nesses custos, você talvez precise considerar o risco de comprometimento entre executar um projeto de rede de hub e spoke estrito e executar várias [Gateways de ExpressRoute do Azure](../../../expressroute/expressroute-about-virtual-network-gateways.md) que você se conectar a 'spokes' para ignorar o emparelhamento de rede virtual. No entanto, os Gateways de ExpressRoute do Azure introduz adicionais [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) também. Você também pode encontrar custos adicionais para softwares de terceiros usados para registro, auditoria e monitoramento de tráfego de rede. Dependendo dos custos para troca de dados por meio do emparelhamento de rede virtual no um lado e os custos criados por licenças de software adicionais e Gateways de ExpressRoute do Azure adicionais, você pode decidir para microssegmentação dentro de uma rede virtual usando sub-redes como unidade de isolamento em vez de redes virtuais.


Para obter uma visão geral dos diferentes métodos para atribuir endereços IP, consulte [Tipos de endereços IP e métodos de alocação no Azure](../../../virtual-network/public-ip-addresses.md). 

Para VMs em execução no SAP HANA, é necessário trabalhar com endereços IP estáticos atribuídos. A razão é que alguns atributos de configuração para o HANA referenciam endereços IP.

Os [ Grupos de segurança de rede (NSGs) do Azure ](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) são usados para direcionar tráfego roteado para a instância do SAP HANA ou para a caixa de depósito. Os NSGs e, por fim, os [grupos de segurança de aplicativos](../../../virtual-network/network-security-groups-overview.md#application-security-groups) estão associados à sub-rede SAP HANA e à sub-rede de gerenciamento.

A imagem a seguir mostra uma visão geral de um esquema aproximado de implantação para o SAP HANA de uma arquitetura de rede virtual hub e spoke a seguir:

![Esquema de implantação aproximado para o SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Para implantar o SAP HANA no Azure sem uma conexão site a site, você ainda deseja proteger a instância do SAP HANA da internet pública e ocultá-lo por trás de um proxy de encaminhamento. Neste cenário básico, a implantação se baseia em serviços DNS internos do Azure para resolver nomes de host. Em uma implantação mais complexa, em que endereços IP públicos são usados, os serviços DNS internos do Azure são especialmente importantes. Use os NSGs do Azure e [NVAs Azure](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorar o roteamento da internet em sua arquitetura de rede virtual do Azure no Azure. A imagem a seguir mostra um esquema aproximado para implantar o SAP HANA sem uma conexão site a site em uma arquitetura VNet hub e spoke:
  
![Esquema de implantação aproximado para o SAP HANA sem conexão site a site](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Outra descrição sobre como usar os NVAs do Azure para controlar e monitorar o acesso da Internet sem a arquitetura VNet hub e spoke pode ser encontrada no artigo [Implantar dispositivos virtuais de rede altamente disponíveis](/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurando a infraestrutura do Azure para escalonamento do SAP HANA
Para descobrir os tipos de VM do Azure que são certificados para expansão de OLAP ou de S/4HANA, verifique o [Diretório de hardware do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Uma marca de verificação na coluna “Cluster” indica suporte à expansão. O tipo de aplicativo indica se há suporte para expansão de OLAP ou de S/4HANA. Para obter detalhes sobre os nós certificados em expansão para cada uma das VMs, verifique os detalhes das entradas na SKU de VM específica listada no diretório de hardware do SAP HANA.

Para obter as versões mínimas do sistema operacional para implantar configurações de expansão em VMs do Azure, verifique os detalhes das entradas na SKU de VM específica listada no diretório de hardware do SAP HANA. A partir de uma configuração de expansão OLAP de n nós, um nó funciona como nó mestre. Os outros nós até o limite da certificação atuam como nó de trabalho. Nós em espera adicionais não contam para o número de nós certificados.

>[!NOTE]
> As implantações de expansão de VM do Azure em SAP HANA com o nó em espera somente são possíveis usando o armazenamento do [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Nenhum outro armazenamento certificado do SAP HANA no Azure permite a configuração de nós em espera do SAP HANA.
>

Para /hana/shared, também recomendamos o uso de [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). 

O design básico típico de um único nó em uma configuração de expansão será semelhante a:

![Diagrama que mostra um design básico típico para um único nó em uma configuração de expansão.](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

A configuração básica de um nó de VM para scale-out do SAP HANA é semelhante a:

- Para **/hana/shared**, use o serviço de NFS nativo fornecido por meio do Azure NetApp Files. 
- Todos os outros volumes de disco não são compartilhados entre os diferentes nós e não são baseados em NFS. As configurações de instalação e as etapas para instalações HANA de expansão com **/hana/data** e **/hana/log** não compartilhados são fornecidas mais abaixo neste documento. Para o armazenamento certificado do HANA que pode ser usado, confira o artigo [Configurações de armazenamento de máquina virtual SAP HANA no Azure](./hana-vm-operations-storage.md).


Ao dimensionar os volumes ou discos, você precisa verificar o documento [Requisitos de armazenamento de TDI do SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) quanto ao tamanho necessário, dependendo do número de nós de trabalho. O documento libera uma fórmula que você precisa aplicar para obter a capacidade necessária do volume.

O outro design importante que é exibido nos gráficos da configuração de nó único para uma VM SAP HANA de expansão é a VNet, ou melhor, a configuração de sub-rede. A SAP recomenda uma separação do tráfego de cliente / aplicativo das comunicações entre os nós do HANA. Como mostrado nos gráficos, essa meta é obtida com dois vNICs diferentes conectados à VM. Ambos os vNICs estão em sub-redes diferentes, possuem dois endereços IP diferentes. Você controla o fluxo de tráfego com regras de roteamento usando NSGs ou rotas definidas pelo usuário.

Particularmente no Azure, não há meios e métodos para impor a qualidade do serviço e cotas em vNICs específicos. Como resultado, a separação entre o cliente/aplicativo e a comunicação entre nós não abre nenhuma oportunidade de priorizar um fluxo de tráfego sobre o outro. Em vez disso, a separação continua a ser uma medida de segurança na proteção das comunicações intra-nó das configurações de scale-out.  

>[!NOTE]
>A SAP recomenda a separação do tráfego de rede para o lado do cliente/aplicativo e o tráfego entre nós, conforme descrito neste documento. Portanto, é recomendável colocar uma arquitetura no lugar, como mostrado nos últimos gráficos. Consulte também sua equipe de segurança e conformidade para obter os requisitos que se desviam da recomendação. 
>

Do ponto de vista da rede, a arquitetura de rede mínima necessária seria semelhante a:

![Princípios básicos de expansão de um único nó](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalando a expansão do SAP HANA n Azure
Instalando uma configuração SAP scale-out, você precisa executar etapas aproximadas de:

- Implementar novas infraestruturas de VNet do Azure ou adaptar uma infraestrutura existente
- Implementar novas VMs usando o Armazenamento Premium gerenciado do Azure, volumes de disco Ultra e/ou volumes de NFS com base em ANF
- - Adapte o roteamento de rede para garantir que, por exemplo, a comunicação entre nós entre as VMs não seja roteada por meio de um [NVA](https://azure.microsoft.com/solutions/network-appliances/). 
- Instale o nó mestre SAP HANA.
- Adaptar parâmetros de configuração do nó mestre do SAP HANA
- Continue com a instalação dos nós de trabalho do SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação do SAP HANA na configuração de scale-out
À medida que sua infraestrutura de VM do Azure é implantada e todas as outras preparações são feitas, é necessário instalar as configurações de escalabilidade de saída do SAP HANA nas seguintes etapas:

- Instale o nó mestre do SAP HANA de acordo com a documentação da SAP
- Caso o Armazenamento Premium do Azure ou o armazenamento de disco Ultra seja usado com discos não compartilhados de /hana/data e/hana/log, é necessário alterar o arquivo global.ini e adicionar o parâmetro “basepath_shared = no” ao arquivo global.ini. Esse parâmetro permitirá que o SAP HANA seja executado de modo de expansão sem os volumes “compartilhados” **/hana/data** e **/hana/log** entre os nós. Detalhes são documentados em [Nota SAP # 2080991](https://launchpad.support.sap.com/#/notes/2080991). Se você estiver usando volumes de NFS com base em ANF para /hana/data e /hana/log, não será necessário fazer essa alteração.
- Após a alteração eventual no parâmetro global.ini, reinicie a instância de SAP HANA.
- Adicione nós de trabalho adicionais. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especifique a rede interna para a comunicação entre nós durante a instalação do SAP HANA ou posteriormente, usando, por exemplo, o hdblcm local. Para obter mais documentação, consulte também [SAP Observação #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Mais informações sobre como configurar um sistema de expansão horizontal SAP HANA com nó em espera no SUSE Linux estão disponíveis em [Implantar um sistema de expansão horizontal SAP HANA com nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md). A documentação equivalente do Red Hat pode ser encontrada no artigo [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 para as máquinas virtuais do Azure

Além das certificações de SAP HANA em VMs da série M do Azure, o SAP HANA Dynamic Tiering 2.0 também tem suporte no Microsoft Azure (consulte a documentação do SAP HANA Dynamic Tiering abaixo). Embora não haja nenhuma diferença na instalação do produto ou operação, por exemplo, por meio da ferramenta Cockpit do SAP HANA dentro de uma máquina de Virtual do Azure, há alguns itens importantes, que são obrigatórios para suporte oficial no Azure. Estes pontos-chave são descritos abaixo. Em todo o artigo, a abreviação de "DT 2.0" será usada em vez do nome completo Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 não tem suporte pelo SAP BW ou S4HANA. Casos de uso principal agora são aplicativos nativos do HANA.


### <a name="overview"></a>Visão geral

A figura abaixo fornece uma visão geral sobre o suporte de DT 2.0 no Microsoft Azure. Há um conjunto de requisitos obrigatórios que devem ser seguidos para cumprir a certificação oficial:

- DT 2.0 deve ser instalado em uma VM do Azure dedicada. Pode não ser executada na mesma VM em que o SAP HANA é executado
- O SAP HANA e DT 2.0 VMs devem ser implantados na mesma rede virtual do Azure
- O SAP HANA e DT 2.0 VMs devem ser implantados com a rede acelerada do Azure habilitada
- Tipo de armazenamento para VMs DT 2.0 deve ser o Armazenamento Premium
- Vários discos do Azure devem ser anexados à VM DT 2.0
- É necessário criar um volume distribuído/raid de software (via lvm ou mdadm) usando a distribuição nos discos do Azure

Mais detalhes serão explicados nas seções a seguir.

![Visão geral da arquitetura SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM do Azure dedicada para SAP HANA DT 2.0

No Azure IaaS, DT 2.0 tem suporte apenas em uma VM dedicada. Não é permitido executar DT 2.0 na mesma VM do Azure onde a instância do HANA está em execução. Inicialmente, dois tipos de VMs podem ser usados para executar o SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Consulte a descrição de tipo VM [aqui](../../sizes-memory.md)

Dada a ideia básica do 2.0 DT, que aborda o descarregamento de dados "passivos" para economizar custos, faz sentido usar tamanhos de VM correspondentes. Embora não haja nenhuma regra rígida sobre as combinações possíveis. Isso depende da carga de trabalho de cliente específico.

As configurações recomendadas seriam:

| Tipo de VM do SAP HANA | Tipo de VM do DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs da série M certificadas para SAP HANA com VMs de 2.0 com suporte DT (M64-32ms e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede do Azure e SAP HANA DT 2.0

Instalar DT 2.0 em uma VM dedicada requer a taxa de transferência de rede entre a VM de 2.0 DT e VM do SAP HANA de 10 Gb no mínimo. Portanto, é obrigatório colocar todas as VMs na mesma rede virtual do Azure e habilitar a rede acelerada do Azure.

Consulte informações adicionais sobre a rede acelerada do Azure [aqui](../../../virtual-network/create-vm-accelerated-networking-cli.md)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

Acordo com diretrizes de práticas recomendadas de DT 2.0, a taxa de transferência de e/s de disco deve ser mínimo 50 MB/s por núcleo físico. Observando a especificação para os dois tipos de VM do Azure, que têm suporte para 2.0 DT, o limite de taxa de transferência de E/S para a VM de disco máximo será:

- E32sv3    :   768 MB/s (sem cache), o que significa uma taxa de 48 MB/s por núcleo físico
- M64-32ms  :  1000 MB/s (sem cache), o que significa uma razão de 62,5 MB/s por núcleo físico

É necessário anexar vários discos do Azure para a VM de 2.0 DT e criar um raid de software (distribuição) no nível do sistema operacional para atingir o limite máximo de taxa de transferência de disco por VM. Um único disco do Azure não pode fornecer a taxa de transferência para alcançar o limite máximo de VM em relação a isso. Armazenamento Premium do Azure é obrigatório para executar o DT 2.0. 

- Detalhes sobre os tipos de disco do Azure disponíveis podem ser encontrados [aqui](../../disks-types.md)
- Encontre detalhes sobre como criar um raid de software via mdadm [aqui](/previous-versions/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre como configurar o LVM para criar um volume distribuído para taxa de transferência máxima que pode ser encontrada [aqui](/previous-versions/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, há opções diferentes para alcançar a taxa de transferência máxima de uma VM. Aqui estão as configurações de disco de volume de dados possíveis para cada tipo de VM de 2.0 DT atingir o limite de taxa de transferência da VM. A VM E32sv3 deve ser considerada como um nível de entrada para cargas de trabalho menores. Caso isso resulte em não ser rápido o suficiente, pode ser necessário redimensionar a VM para M64-32ms.
Assim como a VM M64 32ms tem a quantidade de memória, a carga de e/s pode não alcançar o limite, especialmente para cargas de trabalho com uso intensivo de leitura. Portanto, menos discos no conjunto de distribuição podem ser suficientes, dependendo da carga de trabalho específica do cliente. Mas, por segurança, as configurações de disco abaixo foram escolhidas para garantir a máxima taxa de transferência:


| SKU da VM | Configuração de disco 1 | Configuração de disco 2 | Configuração de disco 3 | Configuração de disco 4 | Configuração de disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente no caso da carga de trabalho intensas de leitura ele poderia melhorar o desempenho de e/s para ativar o cache de host do Azure "somente leitura" conforme recomendado para os volumes de dados do software de banco de dados. Enquanto a transação de log de cache de disco do host do Azure deve ser "nenhum". 

Sobre o tamanho do volume de log de um ponto de partida recomendado está uma heurística de 15% do tamanho dos dados. A criação do volume de log pode ser feita usando os tipos de disco do Azure diferente dependendo dos requisitos de custo e a taxa de transferência. Para o volume do log, é necessária uma alta taxa de transferência de E/S.  Caso o tipo de VM M64-32ms seja usado, é obrigatório habilitar o [Acelerador de Gravação](../../how-to-enable-write-accelerator.md). O Acelerador de Gravação do Azure fornece latência de gravação de disco ideal para o log de transações (disponível apenas para a série M). Há alguns itens a considerar, embora, como o número máximo de discos por tipo de VM. Detalhes sobre o Acelerador de Gravação podem ser encontrados [aqui](../../how-to-enable-write-accelerator.md)


A seguir, são apresentados alguns exemplos sobre dimensionamento de volume do log:

| tipo de disco e tamanho do volume de dados | configuração 1 de tipo de disco e volume de log | configuração 2 de tipo de disco e volume de log |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Como para escalabilidade horizontal de SAP HANA, o diretório /hana/shared deve ser compartilhado entre a VM do SAP HANA e VM DT 2.0. A mesma arquitetura quanto ao uso de expansão do SAP HANA dedicado VMs que atuam como um servidor NFS altamente disponível é recomendado. Para fornecer um volume compartilhado de backup, o design idêntico pode ser usado. Mas, cabe ao cliente se HA será necessária ou se é suficiente usar apenas uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para documentação DT 2.0 

- [Instalação do SAP HANA Dynamic Tiering e atualizar guia](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Recursos e tutoriais de camadas do SAP HANA Dynamic Tiering](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [PoC de SAP HANA Dynamic Tiering](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Aprimoramentos do SAP HANA 2.0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações de implantação do SAP HANA em VMs do Azure
As seções a seguir descrevem algumas das operações relacionadas à implantação de sistemas SAP HANA  em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
Os documentos a seguir descrevem como fazer backup e restaurar a sua implantação do SAP HANA:

- [Visão geral de backup do SAP HANA](./sap-hana-backup-guide.md)
- [Backup em nível de arquivo do SAP HANA](./sap-hana-backup-file-level.md)
- [Parâmetro de comparação de instantâneos de armazenamento do SAP HANA](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Inicie e reinicie as VMs que contêm o SAP HANA
Uma característica importante da nuvem pública do Azure é que você será cobrado somente pelos seus minutos de computação. Por exemplo, quando você desligar uma VM que executa o SAP HANA, você será cobrado somente pelos custos de armazenamento durante aquele período. Outro recurso fica disponível quando você especificar endereços IP estáticos para as VMs na sua implantação inicial. Quando você reiniciar uma VM que contém o SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Use o SAProuter para suporte remoto SAP
Se você tem uma conexão site a site entre suas localizações locais e o Azure, e estiver executando componentes SAP, você provavelmente já está executando o SAProuter. Nesse caso, complete os seguintes itens para suporte remoto:

- Manter o endereço IP estático e privado da VM que hospeda o SAP HANA na configuração do SAProuter.
- Configure o NSG da sub-rede que hospeda a VM do HANA para permitir o tráfego pela porta TCP/IP 3299.

Se estiver conectando ao Azure através da Internet e não tiver um roteador SAP para a VM com SAP HANA, será necessário instalar o componente. Instale o SAProuter em uma VM separada na sub-rede de gerenciamento. A imagem a seguir mostra um esquema aproximado para a implantação do SAP HANA sem uma conexão site a site:

![Esquema de implantação aproximado para o SAP HANA sem conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Certifique-se de instalar o SAProuter em uma VM separada e não em sua VM Jumpbox. A VM separada precisa de um endereço IP estático. Para conectar o SAProuter ao SAProuter que é hospedado pela SAP, entre em contato com o SAP para um endereço IP. (O SAProuter que é hospedado pela SAP é a contraparte da instância do SAProuter que você instala em sua VM.) Use o endereço IP da SAP para configurar sua instância do SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para saber mais sobre como configurar e manter conexões de suporte remoto por meio do SAPRouter, confira a [ documentação do SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Se você estiver executando o SUSE Linux Enterprise Server ou Red Hat, será possível estabelecer um cluster do Pacemaker com dispositivos STONITH. Você pode usar os dispositivos para definir a configuração do SAP HANA que usa replicação síncrona com a Replicação de sistema do HANA e failover automático. Mais informações estão listadas na seção “próximas etapas”.

## <a name="next-steps"></a>Próximas etapas
Conheça os artigos listados
- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md)
- [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Alta disponibilidade do SAP HANA nas VMs do Azure no SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
- [Alta disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)