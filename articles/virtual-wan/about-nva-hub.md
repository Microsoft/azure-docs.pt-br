---
title: 'WAN virtual do Azure: sobre a solução de virtualização de rede no Hub'
description: Neste artigo, você aprenderá sobre soluções de virtualização de rede no Hub WAN virtual.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 365ed60e73be9bb2098022fa767f4ae54b93c37c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028073"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Sobre a solução de virtualização de rede em um hub de WAN virtual do Azure (versão prévia)

A WAN virtual do Azure trabalhou com parceiros de rede para criar automação que facilita a conexão de seu CPE (equipamento premisso do cliente) a um gateway de VPN do Azure no Hub virtual. O Azure está trabalhando com parceiros de rede selecionados para permitir que os clientes implantem uma solução de virtualização de rede de terceiros (NVA) diretamente no Hub virtual. Isso permite que os clientes que desejam conectar sua CPE de ramificação à mesma marca NVA no Hub virtual, para que possam aproveitar os recursos de SD-WAN de ponta a ponta.

Redes Barracuda e Cisco Systems são os primeiros parceiros a fornecer o NVAs que pode ser implantado diretamente no Hub WAN virtual.  Consulte [Barracuda CLOUDGEN Wan](https://www.barracuda.com/products/cloudgenwan) e [Cisco Cloud onrampa para várias nuvens](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) para obter a respectiva documentação de produto. O Azure está trabalhando com mais parceiros, portanto, espere ver outras ofertas a seguir.

> [!NOTE]
> Somente as ofertas do NVA que estão disponíveis para serem implantadas no Hub WAN virtual podem ser implantadas no Hub WAN virtual. Eles não podem ser implantados em uma rede virtual arbitrária no Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Como ele funciona?

Os NVAs que estão disponíveis para serem implantados diretamente no Hub de WAN virtual do Azure são projetados especificamente para serem usados no Hub virtual. A oferta NVA é publicada no Azure Marketplace como um aplicativo gerenciado, e os clientes podem implantar a oferta diretamente do Azure Marketplace ou podem implantar a oferta do Hub virtual por meio do portal do Azure.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Visão geral do processo":::

A oferta de NVA de cada parceiro terá uma experiência e uma funcionalidade um pouco diferentes com base em seus requisitos de implantação. No entanto, há algumas coisas que são comuns em todas as ofertas de parceiros para NVA no Hub de WAN virtual.

* Uma experiência de aplicativo gerenciado oferecida por meio do Azure Marketplace.
* Capacidade e cobrança com base em unidade de infraestrutura NVA.
* Métricas de integridade na superfície por meio de Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Aplicativo gerenciado

Todas as ofertas do NVA que estão disponíveis para serem implantadas no Hub WAN virtual terão um **aplicativo gerenciado** que está disponível no Azure Marketplace. Os aplicativos gerenciados permitem que os parceiros façam o seguinte:

* Crie uma experiência de implantação personalizada para seus NVA.
* Forneça um modelo especializado do Resource Manager que permita criar o NVA diretamente no Hub WAN virtual.
* Bille os custos de licenciamento de software diretamente ou por meio do Azure Marketplace.
* Expor propriedades personalizadas e medidores de recurso.

Os parceiros de NVA podem criar recursos diferentes dependendo de sua implantação de dispositivo, licenciamento de configuração e necessidades de gerenciamento. Quando um cliente cria um NVA no Hub de WAN virtual, como todos os aplicativos gerenciados, haverá dois grupos de recursos criados em sua assinatura.

* **Grupo de recursos do cliente** -isso conterá um espaço reservado do aplicativo para o aplicativo gerenciado. Os parceiros podem usar isso para expor quaisquer propriedades de cliente que escolherem aqui.
* **Grupo de recursos gerenciado** -os clientes não podem configurar ou alterar recursos nesse grupo de recursos diretamente, pois isso é controlado pelo editor do aplicativo gerenciado. Esse grupo de recursos conterá o recurso **NetworkVirtualAppliances** .

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Grupos de recursos de aplicativo gerenciado":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Unidades de infraestrutura NVA

Ao criar um NVA no Hub WAN virtual, você deve escolher o número de unidades de infraestrutura do NVA com as quais deseja implantá-lo. Uma **unidade de infraestrutura NVA** é uma unidade de capacidade de largura de banda agregada para um NVA no Hub de WAN virtual. Uma **unidade de infraestrutura NVA** é semelhante a uma [unidade de escala](pricing-concepts.md#scale-unit) de VPN em termos da forma como você imagina a capacidade e o dimensionamento.

* 1 a unidade de infraestrutura NVA representa 500 Mbps de largura de banda agregada para todas as conexões de site de filial que entram nesse NVA, a um custo de $0,25/hora.
* O Azure dá suporte de unidades de infraestrutura NVA de 1-80 para uma determinada implantação de Hub virtual NVA.
* Cada parceiro pode oferecer diferentes pacotes de unidade de infraestrutura NVA que são um subconjunto de todas as configurações de unidade de infraestrutura NVA com suporte.

Semelhante às unidades de escala de VPN, se você escolher *1 unidade de infraestrutura NVA = 500 Mbps*, significa que duas instâncias de redundância serão criadas, cada uma com uma taxa de transferência máxima de 500 Mbps. Por exemplo, se você tiver cinco branches, cada um com 10 Mbps, precisará de um total de 50 Mbps na cabeceira de rede. O planejamento da capacidade agregada do NVA deve ser feito depois de avaliar a capacidade necessária para dar suporte ao número de branches para o Hub.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Processo de configuração de solução de virtualização de rede

Os parceiros trabalharam para fornecer uma experiência que configura o NVA automaticamente como parte do processo de implantação. Depois que o NVA tiver sido provisionado no Hub virtual, qualquer configuração adicional que possa ser necessária para o NVA deverá ser feita por meio do portal do NVA Partners ou do aplicativo de gerenciamento. O acesso direto ao NVA não está disponível.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Recursos de site e conexão com NVAs

Ao contrário das configurações de gateway de VPN do Azure, você não precisa criar recursos de **site** , recursos de **conexão de site a site** ou recursos de **conexão de ponto a site** para conectar seus sites de Branch ao seu NVA no Hub de WAN virtual. Tudo isso é gerenciado por meio do parceiro NVA.

Você ainda precisa criar conexões de Hub para VNet para conectar seu hub WAN virtual a suas redes virtuais do Azure.

## <a name="supported-regions"></a><a name="regions"></a>Regiões com suporte

NVA no Hub virtual está disponível para visualização nas seguintes regiões:

|Região geopolítica | Regiões do Azure|
|---|---|
| América do Norte| Oeste dos EUA, Sul EUA Central, leste dos EUA 2   |
| América do Sul | Brazil South |
| Europa | Europa Ocidental, Sul do Reino Unido|
|  Oriente Médio | Norte dos EAU |
| Ásia | Japan East |
| Austrália | Leste da Austrália |

## <a name="faq"></a>Perguntas frequentes

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Sou um parceiro de dispositivo de rede e gostaria de obter nosso NVA no Hub.  Posso participar deste programa de parceria?

Infelizmente, não temos capacidade de integrar novas ofertas de parceiros no momento. Verifique novamente conosco em novembro!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Posso implantar qualquer NVA do Azure Marketplace no Hub de WAN virtual?

Neste momento, somente o [Barracuda CLOUDGEN Wan](https://aka.ms/BarracudaMarketPlaceOffer) e o [aplicativo Cisco Cloud vWAN](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) estão disponíveis para serem implantados no Hub Wan virtual.

### <a name="what-is-the-cost-of-the-nva"></a>Qual é o custo do NVA?

Você deve comprar uma licença para o NVA do fornecedor do NVA.  Para a licença Barracuda CloudGen WAN NVA da Barracuda, consulte a [página Wan CloudGen do Barracuda](https://www.barracuda.com/products/cloudgenwan). No momento, a Cisco oferece o modelo de licenciamento BYOL (traga sua própria licença) que precisa ser adquirido diretamente da Cisco. Além disso, você também incorrerá em encargos da Microsoft para as unidades de infraestrutura NVA que você consumir e quaisquer outros recursos que usar. Para obter mais informações, consulte [conceitos de preços](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Posso implantar um NVA em um hub básico?

Não. Você deve usar um hub padrão se desejar implantar um NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Posso implantar um NVA em um hub seguro?

Sim. O Barracuda CloudGen WAN pode ser implantado em um hub com o Firewall do Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Posso conectar qualquer dispositivo CPE em minha filial ao Barracuda CloudGen WAN NVA no Hub?

Não. O Barracuda CloudGen WAN só é compatível com os dispositivos Barracuda CPE. Para saber mais sobre os requisitos de WAN do CloudGen, consulte a [página de WAN do CloudGen do Barracuda](https://www.barracuda.com/products/cloudgenwan). Para a Cisco, há vários dispositivos de CPE SD-WAN que são compatíveis. Consulte o [Cisco Cloud Enrampa para documentação de várias nuvens](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) para CPEs compatível.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Quais cenários de roteamento têm suporte com o NVA no Hub?

Todos os cenários de roteamento com suporte pela WAN virtual têm suporte com o NVAs no Hub.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN virtual, consulte o artigo [visão geral da WAN virtual](virtual-wan-about.md) .
