---
title: Arquitetura de rede do SAP HANA do Azure (Instâncias Grandes) | Microsoft Docs
description: arquitetura de rede para implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835749"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (Instâncias Grandes)

A arquitetura dos serviços de rede do Azure é um componente essencial da implantação com êxito dos aplicativos do SAP HANA em Instâncias Grandes. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. É provável que nem todos os sistemas SAP sejam baseados no SAP HANA. A paisagem do SAP é provavelmente um híbrido que usa:

- Sistemas SAP implantados no local. Devido aos tamanhos, esses sistemas atualmente não podem ser hospedados no Azure. Um exemplo é um sistema SAP ERP de produção que é executado no SQL Server (como o banco de dados) e requer mais recursos de CPU ou memória do que as VMs podem fornecer.
- Sistemas SAP baseados em SAP HANA implantados localmente.
- Sistemas SAP implantados em VMs. Esses sistemas podem ser de desenvolvimento, teste, área restrita ou instâncias de produção para qualquer um dos aplicativos baseados no SAP NetWeaver que podem implantar com êxito no Azure (em VMs), com base no consumo de recursos e na demanda de memória. Esses sistemas também podem ser baseados em bancos de dados, como o SQL Server. Para obter mais informações, consulte [suporte a nota SAP n º 1928533 – aplicativos SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E). E esses sistemas podem ser baseados em bancos de dados como o SAP HANA. Para obter mais informações, consulte [Plataformas de IaaS certificadas pelo SAP HANA](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Servidores de aplicativos do SAP implantados no Azure (em VMs) que aproveitam o SAP HANA do Azure (Instâncias Grandes) em carimbos da Instância Grande do Azure.

Uma paisagem do SAP híbrido com quatro ou mais cenários de implantação diferentes é típico. Também há muitos casos de clientes de paisagem do SAP completos que executam no Azure. À medida que as VMs tornam-se mais avançadas, o número de clientes que move todas as soluções SAP no Azure aumenta.

A rede do Azure no contexto dos sistemas SAP implantados no Azure não é complicada. É baseada nos seguintes princípios:

- As redes virtuais do Azure devem estar conectadas ao circuito do ExpressRoute que conecta-se a uma rede local.
- Um circuito do ExpressRoute que conecta-se do local para o Azure normalmente deve ter uma largura de banda de 1 Gbps ou superior. Essa largura de banda mínima permite largura de banda adequada para a transferência de dados entre sistemas locais e sistemas em execução nas VMs. Também permite largura de banda adequada para conexão a sistemas do Azure a partir de usuários locais.
- Todos os sistemas SAP no Azure devem ser configurados em redes virtuais para comunicarem-se entre si.
- O Active Directory e o DNS hospedados no local são estendidos para o Azure por meio do ExpressRoute do local.


> [!NOTE] 
> Com relação à cobrança, somente uma assinatura do Azure pode ser vinculada a apenas um locatário em um carimbo da Instância Grande em uma região do Azure específica. Ao contrário, um único locatário de carimbo da Instância Grande pode ser vinculado a apenas uma assinatura do Azure. Esse requisito é consistente com outros objetos cobráveis no Azure.

Se o SAP HANA do Azure (Instâncias Grandes) for implantado em várias regiões diferentes do Azure, um locatário separado será implantado no carimbo da Instância Grande. É possível executar ambos sob a mesma assinatura do Azure, desde que essas instâncias façam parte da mesma paisagem do SAP. 

> [!IMPORTANT] 
> Somente a implantação do Azure Resource Manager tem suporte com o SAP HANA do Azure (Instâncias Grandes).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais sobre rede virtual

Para conectar uma rede virtual ao ExpressRoute, um gateway do Azure deve ser criado. Para obter mais informações, confira [Sobre os gateways de rede virtual para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway do Azure pode ser usado com ExpressRoute para uma infraestrutura fora do Azure ou para um carimbo da Instância Grande do Azure. Um gateway do Azure também pode ser usado para conectar-se entre redes virtuais. Para obter mais informações, consulte [Configurar uma conexão rede a rede para o Gerenciador de Recursos usando PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É possível conectar o gateway do Azure a um máximo de quatro conexões diferentes do ExpressRoute, desde que essas conexões sejam originadas de diferentes roteadores de borda corporativa da Microsoft. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A taxa de transferência fornecida por um gateway do Azure é diferente para ambos os casos de uso. Para obter mais informações, consulte [Sobre o Gateway de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A taxa de transferência máxima que é possível obter com um gateway de rede virtual é de 10 Gbps usando uma conexão do ExpressRoute. A cópia de arquivos entre uma VM que reside em uma rede virtual e um sistema local (como um único fluxo de cópia) não alcança a taxa de transferência total as diferentes SKUs do gateway. Para aproveitar a largura de banda completa do gateway de rede virtual, use vários fluxos. Ou, será necessário copiar arquivos diferentes em fluxos paralelos de um único arquivo.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para SAP HANA em Instâncias Grandes
A arquitetura de rede para o SAP HANA em Instâncias Grandes pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute para o Azure. Essa parte é o domínio do cliente e é conectada ao Azure por meio do ExpressRoute. Observe o canto inferior direito na figura a seguir.
- Serviços de rede do Azure, conforme abordado anteriormente, com redes virtuais, que também possuem gateways. Essa parte é uma área na qual você precisa localizar os projetos apropriados para os requisitos do aplicativo, segurança e requisitos de conformidade. Se você usa o SAP HANA em Instâncias Grandes, é outro ponto a ser considerado em termos do número de redes virtuais e SKUs do gateway do Azure a escolher. Observe a parte superior direita na figura.
- Conectividade do SAP HANA em Instâncias Grandes através da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo o que você precisa fazer é fornecer alguns intervalos de endereço IP após a implantação dos ativos no SAP HANA em Instâncias Grandes conectar o circuito do ExpressRoute às redes virtuais. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Rede no SAP HANA em Instâncias Grandes, o que geralmente é mais transparente para você.

![Rede virtual conectada ao SAP HANA do Azure (Instâncias Grandes) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

O requisito de que os ativos locais devem conectar-se por meio do ExpressRoute para o Azure não altera porque você utiliza o SAP HANA em Instâncias Grandes. O requisito de ter uma ou várias redes virtuais executando as VMs, que hospedam a camada de aplicativo que conecta-se às instâncias do HANA hospedadas nas unidades do SAP HANA em Instâncias Grandes, também não é alterado. 

As diferenças para implantações do SAP no Azure são:

- As unidades do SAP HANA em Instâncias Grandes do locatário do cliente são conectadas por meio de outro circuito do ExpressRoute às redes virtuais. Para separar as condições de carga, os locais para links do ExpressRoute de redes virtuais e os links entre as redes virtuais e o SAP HANA em Instâncias Grandes não compartilham os mesmos roteadores.
- O perfil de carga de trabalho entre a camada de aplicativo do SAP e o SAP HANA em Instâncias Grandes é de natureza diferente, com muitas solicitações e intermitências pequenas, como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura de aplicativos do SAP é mais sensível à latência de rede que os cenários típicos em que os dados são trocados entre o local e o Azure.
- O gateway de rede virtual possui pelo menos duas conexões do ExpressRoute. Ambas as conexões compartilham a largura de banda máxima dos dados recebidos do gateway de rede virtual.

A latência de rede entre as VMs e as unidades do SAP HANA em Instâncias Grandes pode ser maior que uma latência de viagem de ida e volta típica de rede de VM para VM. Dependendo da região do Azure, os valores medidos podem exceder a latência de ida e volta de 0,7 ms classificada como abaixo da média em [nota SAP 1100926 – perguntas Frequentes: Desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependendo da ferramenta e da região do Azure para medir a latência da viagem de ida e volta da rede entre uma VM do Azure e a unidade de Instância Grande do HANA, a latência medida pode ser até cerca de 2 milissegundos. No entanto, os clientes implantam aplicativos do SAP de produção baseados no SAP HANA com êxito no SAP HANA em Instâncias Grandes. Certifique-se de testar os processos empresariais completamente no SAP HANA em Instâncias Grandes do Azure.
 
Para fornecer latência de rede determinística entre VMs e o SAP HANA em Instâncias Grandes, a escolha da SKU do gateway de rede virtual é essencial. Diferentemente dos padrões de tráfego entre VMs locais e VMs, o padrão de tráfego entre as VMs e o SAP HANA em Instâncias Grandes pode desenvolver pequenas, mas elevadas intermitências de solicitações e volumes de dados a serem transmitidos. Para lidar bem com essas intermitências, é recomendável usar a SKU do gateway UltraPerformance. Para a classe do Tipo II de SKUs do SAP HANA em Instâncias Grandes, o uso da SKU do gateway UltraPerformance como um gateway de rede virtual é obrigatório.

> [!IMPORTANT] 
> Dado o tráfego geral entre o aplicativo do SAP e as camadas do banco de dados, somente as SKUs do gateway HighPerformance ou UltraPerformance para redes virtuais têm suporte para conexão com o SAP HANA do Azure (Instâncias Grandes). Para SKUs do SAP HANA em Instâncias Grandes Tipo II, somente a SKU do gateway UltraPerformance tem suporte como um gateway de rede virtual.



## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura local mostrada anteriormente é conectada por meio do ExpressRoute no Azure. O circuito do ExpressRoute conecta-se a um roteador de borda corporativa. Para obter mais informações, consulte [Visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Após o estabelecimento da rota, ela conecta-se ao backbone do Azure e todas as regiões do Azure são acessíveis.

> [!NOTE] 
> Para executar paisagens do SAP no Azure, conecte o roteador de borda corporativa mais próximo da região do Azure na paisagem do SAP. Os carimbos da Instância Grande do Azure são conectados por meio de dispositivos de roteador de borda corporativa dedicados para minimizar a latência de rede entre VMs em carimbos da Instância Grande e IaaS do Azure.

O gateway de rede virtual para as VMs que hospedam instâncias do aplicativo do SAP é conectado ao circuito do ExpressRoute. A mesma rede virtual é conectada a um roteador de borda corporativa separado dedicado para conectar os carimbos da Instância Grande.

Esse sistema é um exemplo direto de um único sistema SAP. A camada de aplicativo do SAP é hospedada no Azure. O banco de dados do SAP HANA é executado no SAP HANA do Azure (Instâncias Grandes). A hipótese é que a largura de banda do gateway de rede virtual de 2 Gbps ou 10 Gbps não representa um gargalo.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para conectar o SAP HANA do Azure (Instâncias Grandes), a taxa de transferência do gateway de rede virtual poderá tornar-se um gargalo. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também é possível criar uma rede virtual especial que conecta-se ao SAP HANA em Instâncias Grandes para casos como:

- Execução de backups diretamente das instâncias do HANA na Instância Grande do HANA para uma VM no Azure que hospeda compartilhamentos NFS.
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

Use uma rede virtual separada para hospedar as VMs que gerenciam o armazenamento. Esse acordo evita os efeitos de grandes arquivos ou transferência de dados do SAP HANA em Instâncias Grandes para o Azure no gateway de rede virtual que atende às VMs executando a camada de aplicativo do SAP. 

Uma arquitetura de rede mais escalonável:

- Aproveite várias redes virtuais para uma única camada de aplicativo do SAP maior.
- Implante uma rede virtual separada para cada sistema SAP implantado, em comparação à combinação desses sistemas SAP em sub-redes separadas sob a mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantar camada de aplicativo do SAP em várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

A figura mostra a camada de aplicativo do SAP, ou componentes, implantados em várias redes virtuais. Essa configuração introduziu uma sobrecarga de latência inevitável durante a comunicação entre os aplicativos hospedados nessas redes virtuais. Por padrão, o tráfego entre as VMs localizadas em diferentes redes virtuais é roteado pelos roteadores de borda corporativa nessa configuração. A maneira de otimizar e reduzir a latência na comunicação entre duas redes virtuais é pelo emparelhamento de redes virtuais dentro da mesma região. Esse método funcionará mesmo se essas redes virtuais estiverem em assinaturas diferentes. Com o emparelhamento de rede virtual, a comunicação entre as VMs em duas redes virtuais diferentes pode usar o backbone de rede do Azure para comunicarem diretamente entre si. Latência mostra como se as VMs estivessem na mesma rede virtual. O tráfego que endereça intervalos de endereço IP conectados por meio do gateway de rede virtual do Azure é roteado pelo gateway de rede virtual individual da rede virtual. 

Para obter mais informações sobre o emparelhamento de rede virtual, consulte [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Roteamento no Azure

Três considerações de roteamento de rede são importantes para o SAP HANA do Azure (Instâncias Grandes):

* O SAP HANA do Azure (Instâncias Grandes) pode ser acessado somente por meio de VMs e da conexão do ExpressRoute dedicada, não diretamente do local. O acesso direto do local para as unidades do SAP HANA em Instâncias Grandes, conforme fornecido pela Microsoft a você, não é possível imediatamente. As restrições de roteamento transitivas devem-se à arquitetura de rede atual do Azure usada para o SAP HANA em Instâncias Grandes. Alguns clientes de administração e quaisquer aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução no local, não podem conectar ao banco de dados do SAP HANA.

* Se você tiver unidades do SAP HANA em Instâncias Grandes implantadas em duas regiões diferentes do Azure para recuperação de desastre, as mesmas restrições de roteamento transitório serão aplicadas. Em outras palavras, os endereços IP de uma unidade do SAP HANA em Instâncias Grandes em uma região (por exemplo, Oeste dos EUA) não são roteados para uma unidade do SAP HANA em Instâncias Grandes implantada em outra região (por exemplo, Leste dos EUA). Essa restrição é independente do uso do emparelhamento de rede do Azure entre as regiões ou da conexão cruzada dos circuitos do ExpressRoute que conectam as unidades de do SAP HANA em Instâncias Grandes a redes virtuais. Para uma representação gráfica, consulte a figura na seção "Usar unidades do SAP HANA em Instâncias Grandes em várias regiões". Essa restrição, que vem com a arquitetura implantada, proíbe o uso imediato da replicação do sistema HANA como uma funcionalidade de recuperação de desastre.

* As unidades do SAP HANA do Azure (Instâncias Grandes) têm um endereço IP atribuído do intervalo de endereços do pool de IP do servidor que você enviou. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esse endereço IP é acessível por meio das assinaturas do Azure e do ExpressRoute que conecta as redes virtuais ao HANA do Azure (Instâncias Grandes). O endereço IP atribuído fora desse intervalo de endereços do pool de IP do servidor é atribuído diretamente à unidade de hardware. Ele*não* é atribuído pelo NAT, como foi o caso nas primeiras implantações dessa solução. 

> [!NOTE]
> Para superar a restrição no roteamento transitório, conforme explicado nos dois primeiros itens da lista, use componentes adicionais para o roteamento. Componentes que podem ser usados para superar a restrição podem ser:
> 
> * Um proxy reverso para rotear dados, de e para. Por exemplo, F5 BIG-IP, NGINX com o Gerenciador de Tráfego implantado no Azure como uma solução de roteamento de tráfego/firewall virtual.
> * Usar [regras IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) em uma VM Linux para habilitar o roteamento entre localidades locais e as unidades do SAP HANA em Instâncias Grandes ou entre unidades do SAP HANA em Instâncias Grandes em regiões diferentes.
> 
> Esteja ciente de que a implementação e o suporte para soluções personalizadas envolvendo IPTables ou dispositivos de rede de terceiros não são fornecidos pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente usado ou pelo integrador. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade com a Internet do SAP HANA em Instâncias Grandes
O SAP HANA em Instâncias Grandes *não* tem conectividade direta com a Internet. Por exemplo, essa limitação pode restringir a capacidade de registrar a imagem do SO diretamente com o fornecedor do SO. Talvez seja necessário trabalhar com o servidor da Ferramenta de Gerenciamento de Assinaturas do SUSE Linux Enterprise Server ou com o Gerenciador de Assinaturas do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Criptografia de dados entre VMs e o SAP HANA em Instâncias Grandes
Os dados transferidos entre o SAP HANA em Instâncias Grandes e as VMs não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Para obter mais informações, consulta [esta documentação pelo SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usar unidades do SAP HANA em Instâncias Grandes em várias regiões

Você pode ter motivos para implantar o SAP HANA do Azure (Instâncias Grandes) em várias regiões do Azure, exceto para recuperação de desastre. Talvez você queira acessar o SAP HANA em Instâncias Grandes de cada uma das VMs implantadas nas diferentes redes virtuais nas regiões. Os endereços IP atribuídos às diferentes unidades do SAP HANA em Instâncias Grandes não são propagados para além das redes virtuais que estão diretamente conectadas por meio do gateway às instâncias. Como resultado, uma pequena alteração é introduzida no design da rede virtual. Um gateway de rede virtual pode manipular quatro circuitos do ExpressRoute diferentes em diferentes roteadores de borda corporativa. Cada rede virtual conectada a um dos carimbos da Instância Grande pode ser conectada ao carimbo da Instância Grande em outra região do Azure.

![Rede virtual conectada a carimbos da Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as diferentes redes virtuais em ambas as regiões estão conectadas a dois circuitos do ExpressRoute diferentes que são usados para conectar ao SAP HANA do Azure (Instâncias Grandes) em ambas as regiões do Azure. As conexões recém-introduzidas são as linhas vermelhas retangulares. Com essas conexões, fora das redes virtuais, as VMs em execução em uma dessas redes virtuais podem acessar cada uma das diferentes unidades do SAP HANA em Instâncias Grandes implantadas nas duas regiões. Como mostra a figura, presume-se que há duas conexões do ExpressRoute do local para as duas regiões do Azure. Essa organização é recomendada para motivos de recuperação de desastre.

> [!IMPORTANT] 
> Se foram utilizados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.

**Próximas etapas**
- Confira [Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)](hana-storage-architecture.md)