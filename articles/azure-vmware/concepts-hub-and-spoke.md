---
title: Conceito – integrar uma implantação de solução do Azure VMware em uma arquitetura de Hub e spoke
description: Saiba como integrar uma implantação de solução do Azure VMware em uma arquitetura de Hub e spoke no Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062738"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrar a solução do Azure VMware em uma arquitetura de Hub e spoke

Este artigo fornece recomendações para integrar uma implantação de solução do Azure VMware em uma arquitetura de [Hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) existente ou nova no Azure. 


O cenário Hub e spoke assumem um ambiente de nuvem híbrida com cargas de trabalho em:

* Azure nativo usando serviços IaaS ou PaaS
* Solução VMware no Azure 
* vSphere local

## <a name="architecture"></a>Arquitetura

O *Hub* é uma rede virtual do Azure que atua como um ponto central de conectividade com a nuvem privada da solução local e do Azure VMware. Os *spokes* são redes virtuais emparelhadas com o Hub para habilitar a comunicação entre redes virtuais.

O tráfego entre o datacenter local, a nuvem privada da solução Azure VMware e o Hub passa pelas conexões do Azure ExpressRoute. As redes virtuais spoke geralmente contêm cargas de trabalho baseadas em IaaS, mas podem ter serviços de PaaS como [ambiente do serviço de aplicativo](../app-service/environment/intro.md), que tem integração direta com a rede virtual ou outros serviços de PaaS com o [link privado do Azure](../private-link/index.yml) habilitado.

>[!IMPORTANT]
>Você pode usar um Gateway de ExpressRoute existente para se conectar à Solução VMware no Azure, desde que ela não exceda o limite de quatro circuitos de ExpressRoute por rede virtual.  No entanto, para acessar a Solução VMware no Azure do local por meio do ExpressRoute, você precisa ter o Alcance Global do ExpressRoute, pois o gateway do ExpressRoute não fornece roteamento transitivo entre os circuitos conectados.

O diagrama mostra um exemplo de implantação de Hub e spoke no Azure conectado à solução local e do Azure VMware por meio do ExpressRoute Alcance Global.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Implantação do hub de solução do Azure VMware e integração de spoke" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

A arquitetura tem os seguintes componentes principais:

- **Site local:** Datacenters locais do cliente conectados ao Azure por meio de uma conexão do ExpressRoute.

- **Nuvem privada da solução Azure VMware:** Solução do Azure VMware SDDC formada por um ou mais clusters vSphere, cada um com um máximo de 16 hosts.

- **Gateway de ExpressRoute:** Habilita a comunicação entre a nuvem privada da solução do Azure VMware, os serviços compartilhados na rede virtual do Hub e as cargas de trabalho em execução em redes virtuais do spoke.

- **Alcance global ExpressRoute:** Habilita a conectividade entre o local e a nuvem privada da solução Azure VMware. A conectividade entre a solução do Azure VMware e a malha do Azure é apenas por meio do ExpressRoute Alcance Global. Não é possível selecionar nenhuma opção além do caminho rápido do ExpressRoute.  Não há suporte para ExpressRoute Direct.


- **Considerações sobre VPN S2S:** Para implantações de produção de solução do Azure VMware, a VPN S2S do Azure não tem suporte devido a requisitos de rede para o VMware HCX. No entanto, você pode usá-lo para uma implantação de PoC.


- **Rede virtual do Hub:** Atua como o ponto central de conectividade para sua rede local e a nuvem privada da solução Azure VMware.

- **Rede virtual spoke**

    - **Spoke de IaaS:** Um IaaS spoke hospeda cargas de trabalho baseadas no Azure IaaS, incluindo conjuntos de disponibilidade de VM e conjuntos de dimensionamento de máquinas virtuais e os componentes de rede correspondentes.

    - **Spoke de PaaS:** Um spoke de PaaS hospeda serviços de PaaS do Azure usando o endereçamento privado graças ao [ponto de extremidade privado](../private-link/private-endpoint-overview.md) e ao [link privado](../private-link/private-link-overview.md).

- **Firewall do Azure:** Atua como a peça central para segmentar o tráfego entre os spokes e a solução Azure VMware.

- **Gateway de aplicativo:** Expõe e protege aplicativos Web que são executados no Azure IaaS/PaaS ou em VMs (máquinas virtuais) da solução do Azure VMware. Ele se integra a outros serviços como o gerenciamento de API.

## <a name="network-and-security-considerations"></a>Considerações sobre rede e segurança

As conexões do ExpressRoute permitem que o tráfego flua entre o local, a solução do Azure VMware e a malha de rede do Azure. A solução Azure VMware usa o [ExpressRoute alcance global](../expressroute/expressroute-global-reach.md) para implementar essa conectividade.

Como um gateway de ExpressRoute não fornece roteamento transitivo entre seus circuitos conectados, a conectividade local também deve usar o ExpressRoute Alcance Global para se comunicar entre o ambiente vSphere local e a solução Azure VMware. 

* **Fluxo de tráfego de solução local para o Azure VMware**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Fluxo de tráfego de solução local para o Azure VMware" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Solução do Azure VMware para o fluxo de tráfego de VNET do Hub**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Solução do Azure VMware para o fluxo de tráfego de rede virtual do Hub" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Para obter mais informações sobre conceitos de conectividade e rede da solução do Azure VMware, consulte a [documentação do produto da solução Azure VMware](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentação de tráfego

O [Firewall do Azure](../firewall/index.yml) é a peça central da topologia hub e do spoke, implantada na rede virtual do Hub. Use o Firewall do Azure ou outra solução de virtualização de rede com suporte do Azure para estabelecer regras de tráfego e segmentar a comunicação entre os spokes diferentes e as cargas de trabalho da solução Azure VMware.

Crie tabelas de rotas para direcionar o tráfego para o Firewall do Azure.  Para as redes virtuais spoke, crie uma rota que defina a rota padrão para a interface interna do firewall do Azure. Dessa forma, quando uma carga de trabalho na rede virtual precisar acessar o espaço de endereço da solução Azure VMware, o firewall poderá avaliá-la e aplicar a regra de tráfego correspondente para permitir ou negar.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Criar tabelas de rotas para direcionar o tráfego para o Firewall do Azure" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Não há suporte para uma rota com o prefixo de endereço 0.0.0.0/0 na configuração **GatewaySubnet** .

Defina rotas para redes específicas na tabela de rotas correspondente. Por exemplo, as rotas para alcançar os prefixos de IP do gerenciamento de soluções e cargas de trabalho do Azure VMware das cargas de trabalho do spoke e o contrário.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Definir rotas para redes específicas na tabela de rotas correspondente" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Um segundo nível de segmentação de tráfego usando os grupos de segurança de rede dentro dos spokes e o Hub para criar uma política de tráfego mais granular.

> [!NOTE]
> **Tráfego do local para a solução VMware do Azure:** O tráfego entre as cargas de trabalho locais, tanto com base em vSphere quanto em outros, é habilitado por Alcance Global, mas o tráfego não passa pelo firewall do Azure no Hub. Nesse cenário, você deve implementar mecanismos de segmentação de tráfego, seja no local ou na solução do Azure VMware.

### <a name="application-gateway"></a>Gateway de Aplicativo

Aplicativo Azure gateway v1 e v2 foram testados com aplicativos Web que são executados em VMs de solução do Azure VMware como um pool de back-end. Atualmente, o gateway de aplicativo é o único método com suporte para expor aplicativos Web em execução em VMs de solução do Azure VMware para a Internet. Ele também pode expor os aplicativos a usuários internos com segurança.

Para obter mais informações, consulte o artigo específico da solução Azure VMware no [Gateway de aplicativo](./protect-azure-vmware-solution-with-application-gateway.md).

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Segundo nível de segmentação de tráfego usando os grupos de segurança de rede" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Jump box e bastiões do Azure

Acesse o ambiente de solução do Azure VMware com uma caixa de salto, que é uma VM do Windows 10 ou do Windows Server implantada na sub-rede do serviço compartilhado dentro da rede virtual do Hub.

>[!IMPORTANT]
>A bastiões do Azure é o serviço recomendado para se conectar à caixa de salto para evitar a exposição da solução Azure VMware à Internet. Você não pode usar a bastiões do Azure para se conectar às VMs da solução Azure VMware, pois elas não são objetos IaaS do Azure.  

Como prática recomendada de segurança, implante [Microsoft Azure](../bastion/index.yml) serviço de bastiões na rede virtual do Hub. A bastiões do Azure fornece acesso contínuo de RDP e SSH a VMs implantadas no Azure sem a necessidade de provisionar endereços IP públicos para esses recursos. Depois de provisionar o serviço de bastiões do Azure, você pode acessar a VM selecionada no portal do Azure. Depois de estabelecer a conexão, uma nova guia é aberta, mostrando a área de trabalho da caixa de salto e, a partir dessa área de trabalho, você pode acessar o plano de gerenciamento de nuvem privada da solução Azure VMware.

> [!IMPORTANT]
> Não forneça um endereço IP público para a VM da caixa de salto ou expor a porta 3389/TCP para a Internet pública. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rede virtual do hub de bastiões do Azure" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Considerações sobre a resolução de DNS do Azure

Para a resolução de DNS do Azure, há duas opções disponíveis:

-   Use os controladores de domínio implantados no Hub (descrito em [considerações de identidade](#identity-considerations)) como servidores de nomes.

-   Implantar e configurar uma zona privada de DNS do Azure.

A melhor abordagem é combinar ambos para fornecer resolução de nomes confiável para a solução do Azure VMware, no local e no Azure.

Como recomendação de design geral, use a infraestrutura de DNS do Azure existente (nesse caso, o DNS Active Directory integrado) implantado em pelo menos duas VMs do Azure implantadas na rede virtual do Hub e configuradas nas redes virtuais do spoke para usar esses servidores DNS do Azure nas configurações de DNS.

Você pode usar o Azure DNS privado, onde a zona do Azure DNS privado vincula à rede virtual.  Os servidores DNS são usados como resolvedores híbridos com encaminhamento condicional para a solução local ou Azure VMware executando o DNS usando a infraestrutura de DNS privado do Azure do cliente. 

Para gerenciar automaticamente o ciclo de vida dos registros DNS para as VMs implantadas nas redes virtuais spoke, habilite o registro automático. Quando habilitado, o número máximo de zonas DNS privadas é apenas uma. Se desabilitado, o número máximo será 1000.

Os servidores de solução local e do Azure VMware podem ser configurados com encaminhadores condicionais para resolver VMs no Azure para a zona de DNS privado do Azure.

## <a name="identity-considerations"></a>Considerações sobre identidade

Para fins de identidade, a melhor abordagem é implantar pelo menos um controlador de domínio no Hub. Use duas sub-redes de serviço compartilhado no modo distribuído por zona ou um conjunto de disponibilidade de VM. Para obter mais informações sobre como estender seu domínio local Active Directory (AD) para o Azure, consulte [centro de arquitetura do Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Além disso, implante outro controlador de domínio no lado da solução do Azure VMware para atuar como identidade e origem do DNS no ambiente vSphere.

Como prática recomendada, integre o [domínio do AD com o Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
