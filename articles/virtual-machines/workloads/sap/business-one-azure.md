---
title: SAP Business One em Máquinas Virtuais do Microsoft Azure | Microsoft Docs
description: SAP Business One no Azure.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.openlocfilehash: e17739c65c0b80beb1f6fdd09f31897b317d7858
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506881"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One em Máquinas Virtuais do Microsoft Azure
Este documento fornece diretrizes para implantar o SAP Business One em Máquinas Virtuais do Microsoft Azure. A documentação não substitui a documentação de instalação do Business One para SAP. A documentação deve abranger as diretrizes básicas de planejamento e implantação da infraestrutura do Azure para executar os aplicativos do Business One.

O Business One dá suporte para dois bancos de dados diferentes:
- SQL Server - consulte [Nota SAP Nº 928839 - Planejamento de liberação para Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - para obter a matriz de suporte do SAP Business One exata para SAP HANA, confira a [Matriz de Disponibilidade de Produtos SAP](https://support.sap.com/pam)

Com relação ao SQL Server, aplicam-se as considerações básicas de implantação, conforme documentadas na [implantação do DBMS de Máquinas Virtuais do Microsoft Azure para SAP NetWeaver](./dbms_guide_general.md). para SAP HANA, as considerações são mencionadas neste documento.

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa ter um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Microsoft Azure no Windows](../../windows/tutorial-manage-vm.md)
- [Máquinas Virtuais do Microsoft Azure no Linux](../../linux/tutorial-manage-vm.md)
- [Rede do Azure e gerenciamento de redes virtuais com PowerShelll](../../windows/tutorial-virtual-network.md)
- [Rede do Azure e redes virtuais com a CLI](../../linux/tutorial-virtual-network.md)
- [Gerenciar discos do Azure com a CLI do Azure](../../linux/tutorial-manage-disks.md)

Mesmo se você estiver interessado somente no Business One, o documento [Planejamento e implementação de Máquinas Virtuais do Microsoft Azure para SAP NetWeaver](./planning-guide.md) pode ser uma boa fonte de informações.

A suposição é que você, como a instância implantando o SAP Business One está:

- Familiarizado com a instalação do SAP HANA em uma determinada infraestrutura, como uma VM
- Familiarizado com instalação do aplicativo SAP Business One em uma infraestrutura como as VMs do Azure
- Familiarizado com o funcionamento do SAP Business One e o sistema DBMS escolhido
- Familiarizado com a implantação de infraestrutura no Azure

Todas essas áreas não serão abordadas neste documento.

Além da documentação do Azure, você deverá estar ciente das principais Notas SAP, as quais referem-se ao Business One ou as que são Notas centrais do SAP para Business One:

- [528296 - Visão geral de Nota para lançamentos do SAP Business One e produtos relacionados](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Atualizações de lançamento de Nota para SAP Business One 9.2, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Nota central para SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Atualizações de lançamento de Nota para SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Problemas gerais de Nota para SAP Business One 9.3 coletivos](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Consultoria coletiva de Nota para SAP HANA - Tópicos relacionados do SAP Business One, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Arquitetura do Business One
O Business One é um aplicativo que tem duas camadas:

- Uma camada de cliente com um cliente 'fat'
- Uma camada de banco de dados que contém o esquema do banco de dados para um locatário

Uma melhor visão geral de quais componentes estão executando na parte do cliente e quais partes estão executando na parte do servidor está documentada no [Guia do Administrador do SAP Business One](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) 

Como há uma interação crítica de latência pesada entre a camada de cliente e a camada de DBMS, ambas as duas camadas precisam estar localizadas no Azure ao implantar no Azure. é comum que os usuários, então, executem RDS em uma ou várias VMs executando um serviço RDS para os componentes cliente do Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionar VMs para SAP Business One

Em relação ao dimensionamento da(s) VM(s) de cliente, os requisitos de recursos são documentados pela SAP no documento [Guia de Requisitos de Hardware do SAP Business One](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário focar e calcular os requisitos estabelecidos no capítulo 2.4 do documento.

Como Máquinas Virtuais do Microsoft Azure para hospedar os componentes cliente do Business One e o host DBMS, apenas VMs com suporte pelo SAP NetWeaver são permitidas. Para localizar a lista de VMs do Azure com suporte pelo SAP NetWeaver, leia [Nota SAP Nº 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Executando SAP HANA como um back-end do DBMS para negócios One, somente as VMs, que são listadas para negócios no HANA na [lista de plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) do Hana, têm suporte para Hana. Os componentes cliente do Business One não são afetados por essa restrição mais forte para o SAP HANA como sistema DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Lançamentos de sistemas operacionais para uso no SAP Business One

Em princípio, é sempre melhor usar as versões mais recentes do sistema operacional. Especialmente no espaço do Linux, a nova funcionalidade do Azure foi introduzida com diferentes versões menores recentes do Suse e Red Hat. No lado do Windows, o uso do Windows Server 2016 é altamente recomendável.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implantar infraestrutura no Azure para SAP Business One
Nos próximos capítulos, as peças de infraestrutura importantes para a implantação do SAP.

### <a name="azure-network-infrastructure"></a>Infraestrutura de rede do Azure
A infraestrutura de rede necessária para implantar no Azure, depende se você implementa um sistema único do Business One para si próprio. Ou se você é um hoster que hospeda dezenas de sistemas Business One para clientes. Também é possível haver pequenas alterações no design de como você conecta o Azure. Percorrendo diferentes possibilidades, um design em que há uma conectividade de VPN no Azure e onde estende o Active Directory por meio de [VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Azure.

![Configuração de rede simples com Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

A configuração simplificada apresentada introduz várias instâncias de segurança que permitem controlar e limitar roteamento. Isso inicia com 

- O roteador/firewall no lado local do cliente.
- A próxima instância é o [Grupo de Segurança de Rede do Azure](../../../virtual-network/network-security-groups-overview.md) que é possível usar para introduzir regras de roteamento e segurança para a VNet do Azure na qual executa a configuração do SAP Business.
- Para evitar que usuários do cliente Business One também vejam o servidor que executa o servidor do Business One, o qual executa o banco de dados, separe a VM que hospeda o cliente Business One e o servidor do Business Onde em duas sub-redes diferentes dentro da VNet.
- Você usaria o NSG do Azure atribuído às duas sub-redes diferentes novamente para limitar o acesso ao servidor do Business One.

Uma versão mais sofisticada de uma configuração de rede do Azure é baseada nas [melhores práticas documentadas de arquitetura spoke e hub](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) do Azure. O padrão de arquitetura spoke e hub alteraria a primeira configuração simplificada para uma semelhante a esta:


![Configuração de spoke e hub com Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Nos casos em que os usuários estão conectando através da Internet sem conectividade privada no Azure, o design da rede no Azure deverá estar alinhado com os princípios documentados na arquitetura de referência do Azure para [DMZ entre Azure e a Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Servidor de banco de dados do Business One
Para o tipo de banco de dados, o SQL Server e o SAP HANA estão disponíveis. Independentemente do DBMS, você deve ler as [Considerações sobre o documento para a implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](./dbms_guide_general.md) para obter uma compreensão geral das implantações de DBMS em VMs do Azure e os tópicos de rede e armazenamento relacionados.

Embora enfatizado nos documentos de banco de dados específicos e genéricos, é necessário que você esteja familiarizado com:

- [Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](../../availability.md) e [Gerenciar a disponibilidade de máquinas virtuais do Linux no Azure](../../availability.md)
- [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Esses documentos devem ajudá-lo a decidir sobre a seleção de tipos de armazenamento e a configuração de alta disponibilidade.

Em princípio, é necessário:

- Use os SSDs Premium no HDDs Padrão. Para saber mais sobre os tipos de disco disponíveis, confira o artigo [Escolher um tipo de disco](../../disks-types.md)
- Usar discos gerenciados do Azure em discos não gerenciados
- Certificar-se de que há taxa de transferência de E/S e IOPS suficientes configurados com a configuração de disco
- Combinar volume /hana/log e /hana/data para ter uma configuração de armazenamento econômica


#### <a name="sql-server-as-dbms"></a>SQL Server como DBMS
Para implantar o SQL Server como DBMS para Business One, consulte o documento [Implantação de DBMS de Máquinas Virtuais do Microsoft Azure do SQL Server para SAP NetWeaver](./dbms_guide_sqlserver.md). 

Estimativas aproximadas de dimensionamento para o lado de DBMS para SQL Server são:

| Número de usuários | vCPUs | Memória | Exemplo de tipos de VM |
| --- | --- | --- | --- |
| até 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| até 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| até 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| até 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

O dimensionamento listado acima deve dar uma ideia de por onde iniciar. Pode ser que você precise de menos ou mais recursos, caso em que uma adaptação no Azure é fácil. Uma mudança entre os tipos de VM é possível com apenas um reinício da VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA como DBMS
Ao usar SAP HANA como DBMS, as seções a seguir devem seguir as considerações do documento [guia de operações do SAP HANA no Azure](./hana-vm-operations.md).

Para configurações de alta disponibilidade e recuperação de desastre em torno do SAP HANA como banco de dados do Business One no Azure, leia a documentação [Alta disponibilidade do SAP HANA para Máquinas Virtuais do Microsoft Azure](./sap-hana-availability-overview.md) e a documentação indicada a partir desse documento.

Para as estratégias de backup e restauração do SAP HANA, é necessário ler o documento [Guia de backup para SAP HANA em Máquinas Virtuais do Microsoft Azure](./sap-hana-backup-guide.md) e a documentação indicada a partir desse documento.

 
### <a name="business-one-client-server"></a>Servidor do cliente Business One
Para esses componentes, considerações de armazenamento não são a principal preocupação. no entanto, você quer ter uma plataforma confiável. Portanto, é necessário usar o Armazenamento Premium do Azure para essa VM, mesmo para o VHD de base. Dimensionar a VM, com os dados fornecidos no [Guia de Requisitos de Hardware do SAP Business One](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário focar e calcular os requisitos estabelecidos no capítulo 2.4 do documento. Ao calcular os requisitos, é necessário compará-los com os documentos a seguir para encontrar a VM ideal às suas necessidades:

- [Tamanhos das máquinas virtuais do Windows no Azure](../../sizes.md)
- [Observação SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Compare o número de CPUs e memória necessários ao que é documentado pela Microsoft. Além disso, lembre-se da taxa de transferência de rede ao escolher as VMs.
