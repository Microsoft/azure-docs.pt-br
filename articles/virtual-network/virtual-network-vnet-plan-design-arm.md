---
title: Guia de design e planejamento de Rede Virtual do Azure (VNet) | Microsoft Docs
description: Aprenda a planejar e projetar redes virtuais no Azure com base em seus requisitos de isolamento, conectividade e local.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 9a0126235c9ff3fec05d7709bdee95ab4832a33b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planejar e projetar redes virtuais do Azure
Criar uma rede virtual para fazer experiências com ela é bastante simples, mas as chances são: você implantará múltiplas redes virtuais ao longo do tempo para dar suporte às necessidades de produção da sua organização. Com algum planejamento e design, você poderá implantar redes virtuais e conectar-se aos recursos de que precisa com mais eficiência. Se você não estiver familiarizado com redes virtuais, recomenda-se que você [saiba mais sobre redes virtuais](virtual-networks-overview.md) e [como implantar](virtual-networks-create-vnet-arm-pportal.md) uma antes de continuar.

## <a name="plan"></a>Plano
Uma compreensão completa de assinaturas do Azure, regiões e recursos de rede é essencial para o sucesso. Você pode usar a lista de considerações abaixo como ponto de partida. Depois de compreender essas considerações, você pode definir os requisitos para o design de rede.

### <a name="considerations"></a>Considerações
Antes de responder as perguntas planejadas abaixo, considere o seguinte:

* Tudo que você cria no Azure é composto de um ou mais recursos. Uma máquina virtual (VM) é um recurso, a interface de adaptador de rede (NIC) usada por uma máquina virtual é um recurso, o endereço IP público usado por uma NIC é um recurso, a NIC que está conectada à rede virtual é um recurso.
* Criar recursos dentro de uma [Região do Azure](https://azure.microsoft.com/regions/#services) e assinatura. Os recursos só poderão ser conectados a uma rede virtual que existir na mesma região e assinatura em que o recurso está.
* Você pode conectar redes virtuais entre si usando:
    * **[Emparelhamento de rede virtual](virtual-network-peering-overview.md)**: as redes virtuais emparelhadas devem existir na mesma região do Azure. A largura de banda entre os recursos em redes virtuais emparelhadas é igual a como se os recursos estivessem conectados à mesma rede virtual.
    * **Um [Gateway de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) do Azure**: as redes virtuais podem estar na mesma região ou em regiões diferentes do Azure. A largura de banda entre os recursos em redes virtuais conectadas por meio de um Gateway de VPN é limitada pela largura de banda do Gateway de VPN.
* Você pode conectar redes virtuais à sua rede local usando uma das [opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) disponíveis no Azure.
* Recursos diferentes podem ser agrupados em [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), facilitando o gerenciamento de recursos como uma unidade. Um grupo de recursos pode conter recursos de várias regiões, desde que os recursos pertençam à mesma assinatura.

### <a name="define-requirements"></a>Definir requisitos
Use as perguntas a seguir como ponto de partida para o design de rede do Azure.    

1. Quais locais do Azure serão usados para hospedar redes virtuais?
2. Você precisa fornecer comunicação entre esses locais do Azure?
3. Você precisa fornecer comunicação entre suas redes virtuais do Azure e seus datacenters locais?
4. Quantas VMs de IaaS (Infraestrutura como serviço), funções de serviços de nuvem e aplicativos Web são necessários para sua solução?
5. Você precisa isolar o tráfego com base em grupos de VMs (ou seja, servidores de web de front-end e servidores de banco de dados de back-end)?
6. Você precisa controlar o fluxo de tráfego usando dispositivos virtuais?
7. Os usuários precisam de diferentes conjuntos de permissões para diferentes recursos do Azure?

### <a name="understand-vnet-and-subnet-properties"></a>Entender as propriedades de rede virtual e sub-rede
Os recursos de rede virtual e sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNet é caracterizada por uma coleção de espaços de endereços, definidos como blocos CIDR.

> [!NOTE]
> Os administradores de rede estão familiarizados com a notação CIDR. Se não estiver familiarizado com o CIDR, [saiba mais sobre ele](http://whatismyipaddress.com/cidr).
>
>

As VNets contêm as propriedades a seguir.

| Propriedade | Descrição | Restrições |
| --- | --- | --- |
| **name** |Nome da VNet |Cadeia de caracteres de até 80 caracteres. Pode conter letras, números, sublinhados, pontos ou hifens. Deve começar com uma letra ou número. Deve terminar com uma letra, número ou sublinhado. Pode contém letras maiúsculas ou minúsculas. |
| **local** |Local do Azure (também conhecido como região). |Deve ser um dos locais do Azure válidos. |
| **addressSpace** |Coleção de prefixos de endereços que compõem a notação CIDR da rede virtual. |Deve ser uma matriz de blocos de endereços CIDR válidos, incluindo intervalos de endereços IP públicos. |
| **sub-redes** |Coleção de sub-redes que compõem a VNet |Consulte a tabela de propriedades de sub-rede abaixo. |
| **dhcpOptions** |Objeto que contém uma única propriedade necessária denominada **dnsServers**. | |
| **dnsServers** |Matriz de servidores DNS usados pela rede virtual. Se nenhum servidor for especificado, a resolução de nome interno do Azure será usada. |Deve ser uma matriz de até 10 servidores DNS, por endereço IP. |

Uma sub-rede é um recurso filho de uma VNet e ajuda a definir segmentos de espaços de endereços em um bloco CIDR, usando prefixos de endereços IP. As NICs podem ser adicionadas a sub-redes e conectadas a VMs, fornecendo conectividade para diversas cargas de trabalho.

As sub-redes contêm as propriedades a seguir.

| Propriedade | Descrição | Restrições |
| --- | --- | --- |
| **name** |Nome da sub-rede |Cadeia de caracteres de até 80 caracteres. Pode conter letras, números, sublinhados, pontos ou hifens. Deve começar com uma letra ou número. Deve terminar com uma letra, número ou sublinhado. Pode contém letras maiúsculas ou minúsculas. |
| **local** |Local do Azure (também conhecido como região). |Deve ser um dos locais do Azure válidos. |
| **addressPrefix** |Prefixo de endereço único que compõe a sub-rede na notação CIDR |Deve ser um único bloco CIDR que faz parte de um dos espaços de endereço da rede virtual. |
| **networkSecurityGroup** |NSG aplicado à sub-rede | |
| **routeTable** |Tabela de rotas aplicada à sub-rede | |
| **ipConfigurations** |Coleção de objetos de configuração IP usada pelas NICs conectadas à sub-rede | |

### <a name="name-resolution"></a>Resolução de nomes
Por padrão, sua VNet usa a [Resolução de nomes fornecida pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md) para resolver nomes dentro da VNet e na Internet pública. No entanto, se você conectar suas redes virtuais aos seus centros de dados local, você precisa fornecer [seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) para resolver nomes entre suas redes.  

### <a name="limits"></a>limites
Analise os limites de rede no artigo dos [limites do Azure](../azure-subscription-service-limits.md#networking-limits) para garantir que seu design não entre em conflito com qualquer um dos limites. Alguns limites podem ser aumentados abrindo um tíquete de suporte.

### <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Você pode usar o [RBAC do Azure](../active-directory/role-based-access-built-in-roles.md) para controlar o nível de acesso que usuários diferentes podem ter a recursos diferentes no Azure. Dessa forma, você pode separar o trabalho realizado pela equipe de acordo com suas necessidades.

Conforme as redes virtuais são compreendidas, os usuários na função **Colaborador de Rede** têm controle total sobre os recursos de rede virtual do Azure Resource Manager. Da mesma forma, os usuários na função **Colaborador de Rede Clássico** têm controle total sobre os recursos de rede virtual clássica.

> [!NOTE]
> Você também pode [criar suas próprias funções](../active-directory/role-based-access-control-configure.md) para separar suas necessidades administrativas.
>
>

## <a name="design"></a>Design
Se você souber as respostas às perguntas da seção [Planejar](#Plan) , examine o seguinte antes de definir suas redes virtuais.

### <a name="number-of-subscriptions-and-vnets"></a>Número de assinaturas e redes virtuais
Considere criar várias redes virtuais nos seguintes cenários:

* **VMs que precisam ser colocadas em diferentes locais do Azure**. As redes virtuais no Azure são regionais. Eles não podem abranger locais. Portanto, é necessário pelo menos uma rede virtual para cada local do Azure em que você deseja VMs de host.
* **Cargas de trabalho que precisam ser completamente isoladas uma da outra**. Você pode criar redes virtuais separadas, que até mesmo usam os mesmo espaços de endereço IP, para isolar as diferentes cargas de trabalho umas das outras.

Tenha em mente que os limites que você pode ver acima são por região, por assinatura. Isso significa que você pode usar várias assinaturas para aumentar o limite de recursos que você pode manter no Azure. Você pode usar uma VPN site a site ou um circuito de ExpressRoute para conectar redes virtuais em assinaturas diferentes.

### <a name="subscription-and-vnet-design-patterns"></a>Assinatura e padrões de design de rede virtual
A tabela a seguir mostra alguns padrões de design comuns para usar assinaturas e redes virtuais.

| Cenário | Diagrama | Prós | Contras |
| --- | --- | --- | --- |
| Assinatura única, duas redes virtuais por aplicativo |![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Somente uma assinatura para gerenciar. |Número máximo de VNets por região do Azure. Você precisa de mais inscrições depois disso. Leia o artigo [Limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes. |
| Uma assinatura por aplicativo, duas redes virtuais por aplicativo |![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Usa apenas duas redes virtuais por assinatura. |Mais difícil de gerenciar quando há muitos aplicativos. |
| Uma assinatura por unidade de negócios, duas redes virtuais por aplicativo. |![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Equilíbrio entre o número de assinaturas e as redes virtuais. |Número máximo de VNets por unidade de negócios (assinatura). Leia o artigo [Limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes. |
| Uma assinatura por unidade de negócios, duas redes virtuais por grupo de aplicativos. |![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Equilíbrio entre o número de assinaturas e as redes virtuais. |Os aplicativos devem ser isolados usando sub-redes e NSGs. |

### <a name="number-of-subnets"></a>Número de sub-redes
Você deve considerar várias sub-redes em uma rede virtual nos seguintes cenários:

* **Não há endereços IP particulares para todas as NICs em uma sub-rede**. Se o seu espaço de endereço de sub-rede não contém endereços IP suficientes para o número de NICs na sub-rede, você precisa criar várias sub-redes. Tenha em mente que o Azure reserva 5 endereços IP privados de cada sub-rede que não podem ser usados: os endereços e o sobrenome do espaço de endereço (para o endereço de sub-rede e multicast) e 3 endereços a serem usados internamente (para fins de DHCP e DNS).
* **Segurança**. Você pode usar sub-redes para separar grupos de VMs uns dos outros para cargas de trabalho que têm uma estrutura multi-camadas e aplicar diferentes [Grupos de segurança de rede (NSGs)](virtual-networks-nsg.md#subnets) para essas sub-redes.
* **Conectividade híbrida**. Você pode usar gateways de VPN e circuitos do ExpressRoute para [conectar](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) suas redes virtuais entre si e seus datacenters locais. Gateways de VPN e circuitos de ExpressRoute exigem a criação de uma sub-rede própria.
* **Dispositivos virtuais**. Você pode usar um dispositivo virtual, como um firewall, acelerador de WAN ou gateway de VPN em uma rede virtual do Azure. Quando você fizer isso, precisará [rotear o tráfego](virtual-networks-udr-overview.md) para esses dispositivos e isolá-los em sua própria sub-rede.

### <a name="subnet-and-nsg-design-patterns"></a>Padrões de design de sub-rede e NSG
A tabela a seguir mostra alguns padrões de design comum para o uso de sub-redes.

| Cenário | Diagrama | Prós | Contras |
| --- | --- | --- | --- |
| Sub-rede única, NSGs por camada de aplicativo, por aplicativo |![Sub-rede única](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Apenas uma sub-rede para gerenciar. |Vários NSGs necessários para isolar cada aplicativo. |
| Uma sub-rede por aplicativo, NSGs por camada de aplicativo |![Sub-rede por aplicativo](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Menos NSGs para gerenciar. |Várias sub-redes para gerenciar. |
| Uma sub-rede por camada de aplicativo, NSGs por aplicativo. |![Sub-rede por camada](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Equilíbrio entre o número de sub-redes e de NSGs. |Número máximo de NSGs por assinatura. Leia o artigo [Limites de Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes. |
| Uma sub-rede por camada de aplicativo, por aplicativo, NSGs por sub-rede |![Sub-rede por camada por aplicativo](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Possivelmente menor número de NSGs. |Várias sub-redes para gerenciar. |

## <a name="sample-design"></a>Projeto de amostra
Para ilustrar o aplicativo das informações neste artigo, considere o cenário a seguir.

Você trabalha para uma empresa que tem 2 datacenters na América do Norte e dois datacenters na Europa. Você identificou 6 aplicativos voltados para clientes diferentes mantidos por 2 unidades diferentes de negócios que você deseja migrar para o Azure como um piloto. A arquitetura básica para os aplicativos é a seguinte:

* App1, App2, App3 e App4 são aplicativos Web hospedados em servidores Linux executando o Ubuntu. Cada aplicativo se conecta a um servidor de aplicativos separado que hospeda os serviços RESTful em servidores Linux. Os serviços RESTful se conectam a um banco de dados do MySQL de back-end.
* App5 e App6 são aplicativos Web hospedados em servidores Windows que executam o Windows Server 2012 R2. Cada aplicativo se conecta a um banco de dados do SQL Server de back-end.
* Atualmente, todos os aplicativos são hospedados em um dos datacenters da empresa na América do Norte.
* Os datacenters locais usam o espaço de endereço 10.0.0.0/8.

Você precisa projetar uma solução de rede virtual que atenda aos seguintes requisitos:

* Cada unidade de negócios não será afetada por consumo de recursos de outras unidades de negócios.
* Você deve minimizar a quantidade de redes virtuais e sub-redes para facilitar o gerenciamento.
* Cada unidade de negócios deve ter uma única rede virtual de teste/desenvolvimento usada para todos os aplicativos.
* Cada aplicativo é hospedado em 2 datacenters diferentes do Azure por continente (América do Norte e Europa).
* Cada aplicativo é completamente isolado do outro.
* Cada aplicativo pode ser acessado por clientes pela Internet usando HTTP.
* Cada aplicativo pode ser acessado por usuários conectados para os datacenters locais usando um túnel criptografado.
* A conexão aos datacenters locais deve usar dispositivos VPN existentes.
* O grupo de rede da empresa deve ter controle total sobre a configuração de rede virtual.
* Os desenvolvedores em cada unidade de negócios somente devem ser capazes de implantar máquinas virtuais em sub-redes existentes.
* Todos os aplicativos serão migrados como estão no Azure (arrastar e deslocar).
* Os bancos de dados em cada local devem ser replicados para outros locais do Azure uma vez por dia.
* Cada aplicativo deve usar 5 servidores web de front-end, 2 servidores de aplicativos (quando necessário) e 2 servidores de banco de dados.

### <a name="plan"></a>Plano
Você deve iniciar seu planejamento de design respondendo à pergunta na seção [Definir requisitos](#Define-requirements) conforme mostrado abaixo.

1. Quais locais do Azure serão usados para hospedar redes virtuais?

    2 locais na América do Norte e 2 locais na Europa. Você deve escolher aqueles com base na localização física dos datacenters existentes locais. Dessa forma, sua conexão em seus locais físicos no Azure terá uma melhor latência.
2. Você precisa fornecer comunicação entre esses locais do Azure?

    Sim. Como os bancos de dados devem ser replicados para todos os locais.
3. Você precisa fornecer comunicação entre o as rede(s) virtual(is) do Azure e o(s) datacenter(s) locais?

    Sim. Pois os usuários conectados aos datacenters locais devem ser capazes de acessar os aplicativos por meio de um túnel criptografado.
4. Quantas VMs de IaaS você precisa para sua solução?

    200 VMs de IaaS. App1, App2, App3 e App4 exigem 5 servidores Web cada, 2 servidores de aplicativos cada e 2 servidores de banco de dados cada. Isso é um total de 9 VMs de IaaS por aplicativos ou 36 VMs de IaaS. App5 e App6 exigem 5 servidores web e 2 servidores de banco de dados cada. Isso é um total de 7 VMs de IaaS por aplicativos ou 14 VMs de IaaS. Portanto, você precisa de 50 VMs de IaaS para todos os aplicativos em cada região do Azure. Como precisamos usar 4 regiões, haverá 200 VMs de IaaS.

    Você também precisa fornecer os servidores DNS em cada rede virtual ou em seus datacenters locais para resolver o nome entre suas VMs de IaaS do Azure e sua rede local.
5. Você precisa isolar o tráfego com base em grupos de VMs (ou seja, servidores de web de front-end e servidores de banco de dados de back-end)?

    Sim. Cada aplicativo deve ser completamente isolado um do outro, e cada camada do aplicativo também deve ser isolada.
6. Você precisa controlar o fluxo de tráfego usando dispositivos virtuais?

    Não. Dispositivos virtuais podem ser usados para fornecer mais controle sobre o fluxo de tráfego, incluindo logs mais detalhados de plano de dados.
7. Os usuários precisam de diferentes conjuntos de permissões para diferentes recursos do Azure?

    Sim. A equipe de rede precisa de controle total sobre as configurações de rede virtual, enquanto os desenvolvedores somente devem ser capazes de implantar suas VMs em sub-redes já existentes.

### <a name="design"></a>Design
Você deve seguir o design especificando NSGs, redes virtuais, sub-redes e assinaturas. Discutiremos NSGs aqui, mas você deve saber mais sobre [NSGs](virtual-networks-nsg.md) antes de concluir o design.

**Número de assinaturas e redes virtuais**

Os requisitos a seguir estão relacionados a assinaturas e redes virtuais:

* Cada unidade de negócios não será afetada por consumo de recursos de outras unidades de negócios.
* Você deve minimizar a quantidade de redes virtuais e sub-redes.
* Cada unidade de negócios deve ter uma única rede virtual de teste/desenvolvimento usada para todos os aplicativos.
* Cada aplicativo é hospedado em 2 datacenters diferentes do Azure por continente (América do Norte e Europa).

De acordo com esses requisitos, é necessário ter uma assinatura para cada unidade de negócios. Dessa forma, o consumo de recursos de uma unidade de negócios não contará até os limites para outras unidades de negócios. E como você deseja minimizar o número de redes virtuais, você deve considerar o uso de **uma assinatura por unidade de negócios, duas redes virtuais por grupo de aplicativos** padrão conforme mostrado abaixo.

![Assinatura única](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Você também precisa especificar o espaço de endereço para cada rede virtual. Já que você precisa de conectividade entre os datacenters locais e as regiões do Azure, o espaço de endereço utilizado para as Redes Virtuais do Azure não pode estar em conflito com a rede local, e o espaço de endereço utilizado por cada Rede Virtual não deve estar em conflito com outras Redes Virtuais existentes. Você pode usar os espaços de endereço na tabela a seguir para atender a esses requisitos.  

| **Assinatura** | **Rede virtual** | **Região do Azure** | **Espaço de endereço** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |Oeste dos EUA |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Leste dos EUA |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Norte da Europa |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europa Ocidental |172.19.0.0/16 |
| BU1 |TestDevBU1 |Oeste dos EUA |172.20.0.0/16 |
| BU2 |TestDevBU2 |Oeste dos EUA |172.21.0.0/16 |
| BU2 |ProdBU2US1 |Oeste dos EUA |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Leste dos EUA |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Norte da Europa |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europa Ocidental |172.25.0.0/16 |

**Número de sub-redes e NSGs**

Os requisitos a seguir estão relacionados a sub-redes e NSGs:

* Você deve minimizar a quantidade de redes virtuais e sub-redes.
* Cada aplicativo é completamente isolado do outro.
* Cada aplicativo pode ser acessado por clientes pela Internet usando HTTP.
* Cada aplicativo pode ser acessado por usuários conectados para os datacenters locais usando um túnel criptografado.
* A conexão aos datacenters locais deve usar dispositivos VPN existentes.
* Os bancos de dados em cada local devem ser replicados para outros locais do Azure uma vez por dia.

Com base nesses requisitos, você poderia usar uma sub-rede por camada de aplicativo e usar NSGs para filtrar o tráfego por aplicativo. Dessa forma, você só tem 3 sub-redes em cada rede virtual (front-end, camada de aplicativo e a camada de dados) e um NSG por aplicativo por sub-rede. Nesse caso, você deve considerar o uso de um padrão de design de **uma sub-rede por camada de aplicativo, NSGs por aplicativo** . A figura a seguir mostra o uso do padrão de design que representa a rede virtual **ProdBU1US1** .

![Uma sub-rede por camada, um NSG por aplicativo por camada](./media/virtual-network-vnet-plan-design-arm/figure11.png)

No entanto, você também precisa criar uma sub-rede extra para a conectividade VPN entre as redes virtuais e seus datacenters locais. E você precisa especificar o espaço de endereço para cada sub-rede. A figura abaixo mostra um exemplo de solução para a rede virtual **ProdBU1US1** . Você deve replicar esse cenário para cada rede virtual. Cada cor representa um aplicativo diferente.

![Rede virtual de exemplo](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controle de Acesso**

Os requisitos a seguir estão relacionados ao controle de acesso:

* O grupo de rede da empresa deve ter controle total sobre a configuração de rede virtual.
* Os desenvolvedores em cada unidade de negócios somente devem ser capazes de implantar máquinas virtuais em sub-redes existentes.

Com base nesses requisitos, você pode adicionar usuários da equipe de rede para a função interna de **Colaborador de rede** em cada assinatura; e criar uma função personalizada para os desenvolvedores de aplicativos em cada assinatura, dando-lhes direitos para adicionar máquinas virtuais a sub-redes existentes.

## <a name="next-steps"></a>Próximas etapas
* [Implantar uma rede virtual](virtual-networks-create-vnet-arm-template-click.md) com base em um cenário.
* Entender como [balancear a carga](../load-balancer/load-balancer-overview.md) de VMs de IaaS e [gerenciar o roteamento por meio de várias regiões do Azure](../traffic-manager/traffic-manager-overview.md).
* Saiba mais sobre [NSGs e como planejar e projetar](virtual-networks-nsg.md) uma solução NSG.
* Saiba mais sobre os [Locais cruzados e opções de conectividade de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
