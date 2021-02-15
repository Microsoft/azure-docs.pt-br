---
title: Integração de rede virtual de serviços do Azure para isolamento de rede
titlesuffix: Azure Virtual Network
description: Este artigo descreve diferentes métodos de integração de um serviço do Azure a uma rede virtual que permite que você acesse o serviço do Azure com segurança.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785525"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrar serviços do Azure com redes virtuais para isolamento de rede

A integração de rede virtual (VNet) para um serviço do Azure permite que você bloqueie o acesso ao serviço somente para sua infraestrutura de rede virtual. A infraestrutura de VNet também inclui redes virtuais emparelhadas e redes locais.

A integração VNet fornece aos serviços do Azure os benefícios do isolamento de rede e pode ser feito por um ou mais dos seguintes métodos:
- [Implantando instâncias dedicadas do serviço em uma rede virtual](virtual-network-for-azure-services.md). Os serviços podem ser acessados de maneira privada dentro da rede virtual e de redes locais.
- Usando o [ponto de extremidade privado](../private-link/private-endpoint-overview.md) que conecta você de forma privada e segura a um serviço da plataforma [Azure link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o serviço em sua rede virtual.
- Acessar o serviço usando pontos de extremidade públicos, estendendo uma rede virtual para o serviço, por meio de [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md). Pontos de extremidade de serviço permitem que os recursos de serviço da rede virtual sejam protegidos.
- Usando [marcas de serviço](service-tags-overview.md) para permitir ou negar o tráfego para os recursos do Azure de e para pontos de extremidade de IP público.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Implantar serviços dedicados do Azure em redes virtuais

Quando você implanta serviços dedicados do Azure em uma rede virtual, é possível comunicar-se com os recursos de serviço de modo privado, por meio de endereços IP privados.

![Implantar serviços dedicados do Azure em redes virtuais](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A implantação de um serviço dedicado do Azure em sua rede virtual fornece os seguintes recursos:
- Recursos dentro da rede virtual podem se comunicar uns com os outros de modo privado, por meio de endereços IP privados. Por exemplo, transferindo dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na rede virtual.
- Recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados por um VPN Site a Site (Gateway de VPN) ou ExpressRoute.
- Redes virtuais podem ser emparelhadas para permitir que recursos nas redes virtuais se comuniquem entre si usando endereços IP privados.
- As instâncias de serviço em uma rede virtual normalmente são totalmente gerenciadas pelo serviço do Azure. Isso inclui o monitoramento da integridade dos recursos e o dimensionamento com carga.
- Instâncias de serviço são implantadas em uma sub-rede em uma rede virtual. O acesso de rede de entrada e de saída para a sub-rede deve ser aberto por meio de grupos de segurança de rede, segundo as diretrizes fornecidas pelos serviços.
- Determinados serviços também impõem restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço na mesma sub-rede. Verifique as restrições específicas de cada serviço, pois elas podem mudar ao longo do tempo. Exemplos desses serviços são Azure NetApp Files, HSM Dedicado, Instâncias de Contêiner do Azure, Serviço de Aplicativo.
- Opcionalmente, os serviços podem exigir uma sub-rede delegada como um identificador explícito de que uma sub-rede pode hospedar um serviço específico. Ao delegar, os serviços obtêm permissões explícitas para a criação de recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta da API REST em uma rede virtual com uma sub-rede delegada. Uma lista abrangente de serviços que estão usando o modelo de sub-rede delegada pode ser obtida da API Delegações Disponíveis.

Para obter uma lista de serviços que podem ser implantados em uma rede virtual, consulte [implantar serviços dedicados do Azure em redes virtuais](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Vínculo privado e pontos de extremidade privados

Você pode usar pontos de extremidade privados para permitir a entrada de eventos diretamente de sua rede virtual para o recurso do Azure com segurança por meio de um link privado sem passar pela Internet pública. Um ponto de extremidade privado é uma interface de rede especial para um serviço do Azure em sua rede virtual. Quando você cria um ponto de extremidade privado para o recurso do Azure, ele fornece conectividade segura entre clientes em sua rede virtual e o recurso do Azure. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua rede virtual. A conexão entre o ponto de extremidade privado e o serviço do Azure usa um link privado seguro.

O exemplo a seguir mostra o acesso privado de um ponto de extremidade privado de recurso de grade de eventos que fornece conectividade segura entre clientes em uma rede virtual e recurso de grade de eventos.

![Acesso privado do recurso de BD SQL usando o ponto de extremidade privado](./media/network-isolation/architecture-diagram.png)

Para obter mais informações sobre o link privado e uma lista de serviços do Azure com suporte, consulte [o que é o link privado?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Pontos de extremidade de serviço
O ponto de extremidade do serviço VNet fornece conectividade segura e direta aos serviços do Azure por meio de uma rota otimizada na rede de backbone do Azure. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. Os pontos de extremidade de serviço permitem que endereços IP privados na VNet alcancem o ponto final de um serviço do Azure sem precisar de um endereço IP público na VNet.

![Proteção dos serviços do Azure em redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Para obter mais informações, consulte [pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Marcas de serviço

Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. Usando marcas de serviço, você pode definir os controles de acesso à rede em [grupos de segurança de rede](./network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). Ao especificar o nome da marca de serviço (por exemplo, AzureEventGrid) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

![Permitir ou negar o tráfego usando marcas de serviço](./media/network-isolation/service-tags.png)

Você pode usar as marcas de serviço para obter o isolamento da rede e proteger os recursos do Azure da Internet em geral ao acessar os serviços do Azure que tenham pontos de extremidade públicos. Crie regras de entrada/saída para o grupo de segurança de rede a fim de negar o tráfego de/para a **Internet** e permitir o tráfego de/para o **AzureCloud** ou outras [marcas de serviço disponíveis](service-tags-overview.md#available-service-tags) de serviços específicos do Azure.

Para obter mais informações sobre marcas de serviço e serviços do Azure que dão suporte a elas, consulte [visão geral das marcas de serviço](service-tags-overview.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [integrar seu aplicativo a uma rede do Azure](../app-service/web-sites-integrate-with-vnet.md).
- Saiba como [restringir o acesso a recursos usando marcas de serviço](tutorial-restrict-network-access-to-resources.md).
- Saiba como [conectar-se de forma privada a uma conta do Azure Cosmos usando o link privado do Azure](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).