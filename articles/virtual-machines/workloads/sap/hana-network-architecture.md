---
title: Arquitetura de rede do SAP HANA do Azure (Instâncias Grandes) | Microsoft Docs
description: arquitetura de rede para implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01001336e166d5eb2c7dff845b80da2174225a25
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234422"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (Instâncias Grandes)

A arquitetura dos serviços de rede do Azure é um componente essencial da implantação com êxito dos aplicativos do SAP HANA em Instâncias Grandes. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. É provável que nem todos os sistemas de ti estejam localizados no Azure já. Sua estrutura SAP geralmente é híbrida, bem como de um ponto DBMS e um ponto de vista de aplicativos SAP usando uma combinação de NetWeaver, e S/4HANA e SAP HANA e outros DBMS. O Azure oferece serviços diferentes que permitem executar os sistemas diferentes do DBMS, NetWeaver e S/4HANA no Azure. O Azure também oferece tecnologia de rede para que o Azure pareça uma data center virtual para suas implantações de software local

A menos que os sistemas de ti completos estejam hospedados no Azure. A funcionalidade de rede do Azure é usada para conectar o mundo local com seus ativos do Azure para fazer com que o Azure pareça um datacenter virtual do seu. A funcionalidade de rede do Azure usada é: 

- As redes virtuais do Azure são conectadas ao circuito do [ExpressRoute](https://azure.microsoft.com/services/expressroute/) que se conecta aos seus ativos de rede local.
- Um circuito do ExpressRoute que se conecta no local para o Azure deve ter uma largura de banda mínima de [1 Gbps ou superior](https://azure.microsoft.com/pricing/details/expressroute/). Essa largura de banda mínima permite largura de banda adequada para a transferência de dados entre sistemas locais e sistemas em execução nas VMs. Também permite largura de banda adequada para conexão a sistemas do Azure a partir de usuários locais.
- Todos os sistemas SAP no Azure são configurados em redes virtuais para se comunicar entre si.
- Active Directory e DNS hospedados no local são estendidos para o Azure por meio do ExpressRoute do local ou estão sendo executados no Azure.

Para o caso específico da integração de instâncias grandes do HANA à malha de rede data center do Azure, a tecnologia Azure ExpressRoute também é usada


> [!NOTE] 
> Somente uma assinatura do Azure pode ser vinculada a apenas um locatário em um carimbo de instância grande do HANA em uma região específica do Azure. Por outro lado, um único locatário de carimbo de instância grande do HANA pode ser vinculado a apenas uma assinatura do Azure. Esse requisito é consistente com outros objetos cobráveis no Azure.

Se SAP HANA no Azure (instâncias grandes) for implantado em várias regiões diferentes do Azure, um locatário separado será implantado no carimbo de instância grande do HANA. É possível executar ambos sob a mesma assinatura do Azure, desde que essas instâncias façam parte da mesma paisagem do SAP. 

> [!IMPORTANT] 
> Somente o método de implantação Azure Resource Manager tem suporte com SAP HANA no Azure (instâncias grandes).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais sobre rede virtual

Para conectar uma rede virtual ao ExpressRoute, um gateway de ExpressRoute do Azure deve ser criado. Para obter mais informações, consulte [sobre gateways de expressroute para expressroute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway de ExpressRoute do Azure é usado com o ExpressRoute para uma infraestrutura fora do Azure ou para um carimbo de instância grande do Azure. Você pode conectar o gateway do Azure ExpressRoute a um máximo de quatro circuitos diferentes do ExpressRoute, desde que essas conexões venham de diferentes roteadores do Microsoft Enterprise Edge. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A taxa de transferência máxima que você pode obter com um gateway de ExpressRoute é de 10 Gbps usando uma conexão de ExpressRoute. A cópia de arquivos entre uma VM que reside em uma rede virtual e um sistema local (como um único fluxo de cópia) não alcança a taxa de transferência total as diferentes SKUs do gateway. Para aproveitar a largura de banda completa do gateway do ExpressRoute, use vários fluxos. Ou, será necessário copiar arquivos diferentes em fluxos paralelos de um único arquivo.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para SAP HANA em Instâncias Grandes
A arquitetura de rede para o SAP HANA em Instâncias Grandes pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute para o Azure. Essa parte é o domínio do cliente e é conectada ao Azure por meio do ExpressRoute. Esse circuito do Expressroute é totalmente pago por você como um cliente. A largura de banda deve ser grande o suficiente para lidar com o tráfego de rede entre os ativos locais e a região do Azure com a qual você está se conectando. Observe o canto inferior direito na figura a seguir.
- Serviços de rede do Azure, conforme discutido anteriormente, com redes virtuais, que novamente precisam de gateways de ExpressRoute adicionados. Essa parte é uma área na qual você precisa localizar os projetos apropriados para os requisitos do aplicativo, segurança e requisitos de conformidade. Se você usa o SAP HANA em Instâncias Grandes, é outro ponto a ser considerado em termos do número de redes virtuais e SKUs do gateway do Azure a escolher. Observe a parte superior direita na figura.
- Conectividade do SAP HANA em Instâncias Grandes através da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo o que você precisa fazer é fornecer alguns intervalos de endereço IP após a implantação dos ativos no SAP HANA em Instâncias Grandes conectar o circuito do ExpressRoute às redes virtuais. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não há nenhuma taxa adicional para você como um cliente para a conectividade entre a malha de rede data center do Azure e as unidades de instância grande do HANA.
- Rede dentro do carimbo de instância grande do HANA, que é, na maior parte, transparente para você.

![Rede virtual conectada ao SAP HANA do Azure (Instâncias Grandes) e no local](./media/hana-overview-architecture/image1-architecture.png)

O requisito de que os ativos locais devem conectar-se por meio do ExpressRoute para o Azure não altera porque você utiliza o SAP HANA em Instâncias Grandes. O requisito de ter uma ou várias redes virtuais executando as VMs, que hospedam a camada de aplicativo que conecta-se às instâncias do HANA hospedadas nas unidades do SAP HANA em Instâncias Grandes, também não é alterado. 

As diferenças para implantações do SAP no Azure são:

- As unidades do SAP HANA em Instâncias Grandes do locatário do cliente são conectadas por meio de outro circuito do ExpressRoute às redes virtuais. Para separar as condições de carregamento, os circuitos de ExpressRoute do local para a rede virtual do Azure e os circuitos entre as redes virtuais do Azure e as instâncias grandes do HANA não compartilham os mesmos roteadores.
- O perfil de carga de trabalho entre a camada de aplicativo do SAP e o SAP HANA em Instâncias Grandes é de natureza diferente, com muitas solicitações e intermitências pequenas, como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura de aplicativos do SAP é mais sensível à latência de rede que os cenários típicos em que os dados são trocados entre o local e o Azure.
- O gateway de ExpressRoute do Azure tem pelo menos duas conexões de ExpressRoute. Um circuito que está conectado do local e um que está conectado de instâncias grandes do HANA. Isso deixa apenas espaço para outros dois circuitos adicionais de diferentes MSEEs para se conectar ao gateway de ExpressRoute. Essa restrição é independente do uso do caminho rápido do ExpressRoute. Todos os circuitos conectados compartilham a largura de banda máxima para os dados de entrada do gateway de ExpressRoute.

Com a revisão 3 de carimbos de instância grande do HANA, a latência de rede experimentada entre VMs e unidades de instância grande do HANA pode ser maior do que uma latência típica de ida e volta da rede VM para VM. Dependendo da região do Azure, os valores medidos podem exceder a latência de ida e volta de 0,7-MS classificada como abaixo da média na [observação SAP #1100926-FAQ: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E)da rede. Dependendo da ferramenta e da região do Azure para medir a latência da viagem de ida e volta da rede entre uma VM do Azure e a unidade de Instância Grande do HANA, a latência medida pode ser até cerca de 2 milissegundos. No entanto, os clientes implantam aplicativos do SAP de produção baseados no SAP HANA com êxito no SAP HANA em Instâncias Grandes. Certifique-se de testar os processos empresariais completamente no SAP HANA em Instâncias Grandes do Azure. Uma nova funcionalidade, chamada de caminho rápido do ExpressRoute, é capaz de reduzir a latência de rede entre as instâncias grandes do HANA e as VMs da camada de aplicativo no Azure substancialmente (veja abaixo). 

Com a revisão 4 dos carimbos de instância grande do Hana, a latência de rede entre as VMs do Azure que são implantadas em proximidade com o carimbo de instância grande do Hana é uma experiência para [atender à média ou melhor do que a classificação média, conforme documentado na observação SAP #1100926-PERGUNTAS FREQUENTES: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E) de rede se o caminho rápido do Azure ExpressRoute estiver configurado (veja abaixo). Para implantar VMs do Azure de perto às unidades de instância grande do HANA 4, você precisa aproveitar os grupos de [posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). O modo como os grupos de posicionamento de proximidade podem ser usados para localizar a camada de aplicativo SAP no mesmo datacenter do Azure, pois a revisão 4 unidades de instância grande do HANA hospedadas é descrita em [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP ](sap-proximity-placement-scenarios.md).

Para fornecer latência de rede determinística entre VMs e instância grande do HANA, a escolha do SKU do gateway do ExpressRoute é essencial. Diferentemente dos padrões de tráfego entre VMs locais e VMs, o padrão de tráfego entre as VMs e o SAP HANA em Instâncias Grandes pode desenvolver pequenas, mas elevadas intermitências de solicitações e volumes de dados a serem transmitidos. Para lidar bem com essas intermitências, é recomendável usar a SKU do gateway UltraPerformance. Para a classe do tipo II de SKUs de instância grande do HANA, o uso do SKU de gateway UltraPerformance como um gateway ExpressRotue é obrigatório.

> [!IMPORTANT] 
> Dado o tráfego geral entre o aplicativo do SAP e as camadas do banco de dados, somente as SKUs do gateway HighPerformance ou UltraPerformance para redes virtuais têm suporte para conexão com o SAP HANA do Azure (Instâncias Grandes). Para SKUs de tipo II de instância grande do HANA, somente o SKU de gateway UltraPerformance tem suporte como um gateway de ExpressRoute. As exceções se aplicam ao usar o caminho rápido do ExpressRoute (veja abaixo)

### <a name="expressroute-fast-path"></a>Caminho rápido do ExpressRoute
Para diminuir a latência, o caminho rápido do ExpressRoute foi introduzido e lançado em maio de 2019 para a conectividade específica do HANA em instâncias grandes para redes virtuais do Azure que hospedam as VMs do aplicativo SAP. A principal diferença para a solução distribuída até agora, é que os fluxos de dados entre as VMs e as instâncias grandes do HANA não são mais roteados por meio do gateway do ExpressRoute. Em vez disso, as VMs atribuídas nas sub-redes da rede virtual do Azure estão se comunicando diretamente com o roteador do Enterprise Edge dedicado. 

> [!IMPORTANT] 
> A funcionalidade de caminho rápido do ExpressRoute requer que as sub-redes que executam as VMs do aplicativo SAP estejam na mesma rede virtual do Azure que foi conectada às instâncias grandes do HANA. As VMs localizadas em redes virtuais do Azure emparelhadas com a rede virtual do Azure conectada diretamente às unidades de instância grande do HANA não estão se beneficiando do caminho rápido do ExpressRoute. Como resultado típico designs de rede virtual de Hub e spoke, em que os circuitos de ExpressRoute estão se conectando a uma rede virtual de Hub e as redes virtuais que contêm a camada de aplicativo SAP (spokes) estão ficando emparelhadas, a otimização pelo ExpressRoute é rápida O caminho não funcionará. No entanto, o caminho rápido do ExpressRoute não oferece suporte às UDR (regras de roteamento definidas pelo usuário) hoje. Para obter mais informações, consulte [Gateway de rede virtual do ExpressRoute e FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Para obter mais detalhes sobre como configurar o caminho rápido do ExpressRoute, leia o documento [conectar uma rede virtual a instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Um gateway de ExpressRoute UltraPerformance é necessário para que o funcionamento do caminho rápido do ExpressRoute funcione


## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura local mostrada anteriormente é conectada por meio do ExpressRoute no Azure. O circuito do ExpressRoute se conecta a um roteador do Microsoft Enterprise Edge (MSEE). Para obter mais informações, consulte [Visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Depois que a rota é estabelecida, ela se conecta ao backbone do Azure.

> [!NOTE] 
> Para executar paisagens do SAP no Azure, conecte o roteador de borda corporativa mais próximo da região do Azure na paisagem do SAP. Os carimbos de instância grande do HANA são conectados por meio de dispositivos de roteador de borda empresarial dedicados para minimizar a latência de rede entre VMs em carimbos de instância grande do Azure IaaS e HANA.

O gateway de ExpressRoute para as VMs que hospedam instâncias de aplicativos SAP são conectados a um circuito de ExpressRoute que se conecta ao local. A mesma rede virtual é conectada a um roteador de borda corporativa separado dedicado para conectar os carimbos da Instância Grande. Usando o caminho rápido do ExpressRoute, o fluxo de dados do HANA em instâncias grandes para as VMs da camada de aplicativo SAP não é mais roteado por meio do gateway do ExpressRoute e com isso reduz a latência de ida e volta da rede.

Esse sistema é um exemplo direto de um único sistema SAP. A camada de aplicativo do SAP é hospedada no Azure. O banco de dados do SAP HANA é executado no SAP HANA do Azure (Instâncias Grandes). A suposição é que a largura de banda do gateway de ExpressRoute de 2 Gbps ou 10 Gbps de taxa de transferência não representa um afunilamento.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para se conectarem ao SAP HANA no Azure (instâncias grandes), a taxa de transferência do gateway de ExpressRoute poderá se tornar um afunilamento. Ou você deseja isolar sistemas de produção e não produção em diferentes redes virtuais do Azure. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também é possível criar uma rede virtual especial que conecta-se ao SAP HANA em Instâncias Grandes para casos como:

- Execução de backups diretamente das instâncias do HANA na Instância Grande do HANA para uma VM no Azure que hospeda compartilhamentos NFS.
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

Use uma rede virtual separada para hospedar VMs que gerenciam o armazenamento para transferência em massa de dados entre instâncias grandes do HANA e o Azure. Essa organização evita os efeitos de arquivo grande ou transferência de dados do SAP HANA em instâncias grandes para o Azure no gateway de ExpressRoute que atende às VMs que executam a camada de aplicativo SAP. 

Uma arquitetura de rede mais escalonável:

- Aproveite várias redes virtuais para uma única camada de aplicativo do SAP maior.
- Implante uma rede virtual separada para cada sistema SAP implantado, em comparação à combinação desses sistemas SAP em sub-redes separadas sob a mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantar camada de aplicativo do SAP em várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependendo das regras e restrições, você deseja aplicar entre as diferentes redes virtuais que hospedam VMs de diferentes sistemas SAP, você deve emparelhar essas redes virtuais. Para obter mais informações sobre o emparelhamento de rede virtual, consulte [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Roteamento no Azure

Por padrão, a implantação de três considerações de roteamento de rede é importante para SAP HANA no Azure (instâncias grandes):

* SAP HANA no Azure (instâncias grandes) podem ser acessadas somente por meio de VMs do Azure e a conexão do ExpressRoute dedicada, não diretamente do local. O acesso direto do local para as unidades do SAP HANA em Instâncias Grandes, conforme fornecido pela Microsoft a você, não é possível imediatamente. As restrições de roteamento transitivas devem-se à arquitetura de rede atual do Azure usada para o SAP HANA em Instâncias Grandes. Alguns clientes de administração e quaisquer aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução no local, não podem conectar ao banco de dados do SAP HANA. Para exceções, verifique a seção "roteamento direto para o HANA em instâncias grandes".

* Se você tiver unidades de instância grande do HANA implantadas em duas regiões diferentes do Azure para recuperação de desastre, as mesmas restrições de roteamento transitórias são aplicadas no passado. Em outras palavras, os endereços IP de uma unidade de instância grande do HANA em uma região (por exemplo, oeste dos EUA) não eram roteados para uma unidade de instância grande do HANA implantada em outra região (por exemplo, leste dos EUA). Essa restrição era independente do uso do emparelhamento de rede do Azure entre regiões ou da conexão cruzada dos circuitos do ExpressRoute que conectam unidades de instância grande do HANA a redes virtuais. Para uma representação gráfica, consulte a figura na seção "Usar unidades do SAP HANA em Instâncias Grandes em várias regiões". Essa restrição, que vem com a arquitetura implantada, proibiu o uso imediato da replicação de sistema do HANA como uma funcionalidade de recuperação de desastre. Para alterações recentes, procure a seção ' usar unidades de instância grande do HANA em várias regiões '. 

* SAP HANA em unidades do Azure (instâncias grandes) têm um endereço IP atribuído do intervalo de endereços do pool de IPS do servidor que você enviou ao solicitar a implantação da instância grande do HANA. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esse endereço IP pode ser acessado por meio das assinaturas e do circuito do Azure que conecta redes virtuais do Azure a instâncias grandes do HANA. O endereço IP atribuído fora desse intervalo de endereços do pool de IP do servidor é atribuído diretamente à unidade de hardware. Ele*não* é atribuído pelo NAT, como foi o caso nas primeiras implantações dessa solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Roteamento direto para instâncias grandes do HANA
Por padrão, o roteamento transitivo entre as unidades de instância grande do HANA e local ou entre o roteamento de instância grande do HANA implantadas em duas regiões diferentes não funciona. Há várias possibilidades para habilitar esse roteamento transitivo.

- Um proxy reverso para rotear dados, de e para. Por exemplo, F5 BIG-IP, NGINX com o Gerenciador de tráfego implantado na rede virtual do Azure que se conecta ao HANA em instâncias grandes e ao local como uma solução de roteamento de tráfego/firewall virtual.
- Usar [regras IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) em uma VM Linux para habilitar o roteamento entre localidades locais e as unidades do SAP HANA em Instâncias Grandes ou entre unidades do SAP HANA em Instâncias Grandes em regiões diferentes. A VM que executa o IPTables precisa ser implantada na rede virtual do Azure que se conecta às instâncias grandes HANA e ao local. A VM precisa ser dimensionada adequadamente, portanto, a taxa de transferência da rede da VM é suficiente para o tráfego de rede esperado. Para obter detalhes sobre a largura de banda da rede VM, verifique os [tamanhos de artigo das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- O [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/) seria outra solução para habilitar o tráfego direto entre as unidades do local e do SAP Hana em instâncias grandes. 

Todo o tráfego dessas soluções seria roteado por meio de uma rede virtual do Azure e, como tal, o tráfego poderia ser restringido também pelos dispositivos de software usados ou por grupos de segurança de rede do Azure, portanto, esses endereços IP ou intervalos de endereços IP de o local pode ser bloqueado ou autorizado explicitamente a acessar instâncias grandes HANA. 

> [!NOTE]  
> Esteja ciente de que a implementação e o suporte para soluções personalizadas envolvendo IPTables ou dispositivos de rede de terceiros não são fornecidos pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente usado ou pelo integrador. 

#### <a name="express-route-global-reach"></a>Alcance Global de rota expressa
A Microsoft introduziu uma nova funcionalidade chamada [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Alcance Global pode ser usado para instâncias grandes do HANA em dois cenários:

- Habilitar o acesso direto do local para suas unidades de instância grande do HANA implantadas em regiões diferentes
- Habilitar a comunicação direta entre suas unidades de instância grande do HANA implantadas em regiões diferentes


##### <a name="direct-access-from-on-premise"></a>Acesso direto do local
Nas regiões do Azure nas quais Alcance Global é oferecido, você pode solicitar a habilitação da funcionalidade de Alcance Global para o circuito do ExpressRoute que conecta sua rede local à rede virtual do Azure que se conecta às suas unidades de instância grande do HANA também. Há algumas implicações de custo para o lado local do seu circuito do ExpressRoute. Para preços, verifique os preços do [complemento de alcance global](https://azure.microsoft.com/pricing/details/expressroute/). Não há nenhum custo adicional para você relacionado ao circuito que conecta as unidades de instância grande do HANA ao Azure. 

> [!IMPORTANT]  
> No caso do uso de Alcance Global para habilitar o acesso direto entre suas unidades de instância grande do HANA e ativos locais, os dados de rede e o fluxo de controle **não são roteados por meio de redes virtuais do Azure**, mas diretamente entre o Microsoft Enterprise Exchange roteadores. Como resultado, qualquer regra NSG ou ASG, ou qualquer tipo de firewall, NVA ou proxy implantado em uma rede virtual do Azure, não está sendo tocado. **Se você usar o ExpressRoute Alcance Global para habilitar o acesso direto do local para as restrições de unidades de instância grande do HANA e as permissões para acessar as unidades de instância grande do HANA precisam ser definidas em firewalls no lado local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Conectando instâncias grandes do HANA em diferentes regiões do Azure
Da mesma forma, como o ExpressRoute Alcance Global pode ser usado para conexão local com as unidades de instância grande do HANA, ele pode ser usado para conectar locatários de instância grande do duas HANA que são implantados para você em duas regiões diferentes. O isolamento é o circuitos de ExpressRoute que seus locatários de instância grande do HANA estão usando para se conectar ao Azure em ambas as regiões. Não há encargos adicionais para conectar dois locatários de instância grande do HANA que são implantados em duas regiões diferentes. 

> [!IMPORTANT]  
> O fluxo de dados e o fluxo de controle do tráfego de rede entre os diferentes locatários de instância grande do HANA não serão roteados por meio de redes do Azure. Como resultado, você não pode usar a funcionalidade do Azure ou NVAs para impor restrições de comunicação entre seus locatários de instâncias grandes do HANA. 

Para obter mais detalhes sobre como obter o ExpressRoute Alcance Global habilitado, leia o documento [conectar uma rede virtual a instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade com a Internet do SAP HANA em Instâncias Grandes
O SAP HANA em Instâncias Grandes *não* tem conectividade direta com a Internet. Por exemplo, essa limitação pode restringir a capacidade de registrar a imagem do SO diretamente com o fornecedor do SO. Talvez seja necessário trabalhar com o servidor da Ferramenta de Gerenciamento de Assinaturas do SUSE Linux Enterprise Server ou com o Gerenciador de Assinaturas do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Criptografia de dados entre VMs e o SAP HANA em Instâncias Grandes
Os dados transferidos entre o SAP HANA em Instâncias Grandes e as VMs não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Para obter mais informações, consulta [esta documentação pelo SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usar unidades do SAP HANA em Instâncias Grandes em várias regiões

Para perceber a recuperação de desastres set ups, você precisa ter SHANA grandes unidades de instância em várias regiões do Azure. Mesmo com o uso do Azure [emparelhamento vnet global], o roteamento transitivo por padrão não está funcionando entre locatários de instância grande do HANA em duas regiões diferentes. No entanto, Alcance Global abre o caminho de comunicação entre as unidades do SAP HANA em instâncias grandes que você provisionou em duas regiões diferentes. Esse cenário de uso do ExpressRoute Alcance Global permite:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Copiando backups entre unidades de instância grande do HANA em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema


![Rede virtual conectada a carimbos da Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as diferentes redes virtuais em ambas as regiões estão conectadas a dois circuitos de ExpressRoute diferentes que são usados para se conectar a SAP HANA no Azure (instâncias grandes) em ambas as regiões do Azure (linhas cinzas). O motivo para essas duas conexões cruzadas é proteger contra uma interrupção do MSEEs em ambos os lados. O fluxo de comunicação entre as duas redes virtuais nas duas regiões do Azure deve ser tratado sobre o [emparelhamento global](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) das duas redes virtuais nas duas regiões diferentes (linha pontilhada azul). A linha vermelha espessa descreve a conexão de Alcance Global do ExpressRoute, que permite que as unidades de instância grande do HANA de seus locatários em duas regiões diferentes se comuniquem entre si. 

> [!IMPORTANT] 
> Se foram utilizados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.

**Próximas etapas**
- Confira [Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)](hana-storage-architecture.md)