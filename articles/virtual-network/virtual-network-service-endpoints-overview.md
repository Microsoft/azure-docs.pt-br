---
title: Pontos de extremidade de serviço de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como habilitar o acesso direto aos recursos do Azure de uma rede virtual usando pontos de extremidade de serviço.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8926e99db926fc8182e98509c3deff0ccc3d1612
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576660"
---
# <a name="virtual-network-service-endpoints"></a>Pontos de extremidade de serviço de Rede Virtual

O ponto de extremidade do serviço de rede virtual (VNet) fornece conectividade segura e direta aos serviços do Azure por meio de uma rota otimizada pela rede de backbone do Azure. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. Os pontos de extremidade de serviço permitem que endereços IP privados na VNet alcancem o ponto final de um serviço do Azure sem precisar de um endereço IP público na VNet.

Esse recurso está disponível para os seguintes serviços e regiões do Azure. O *Microsoft. \** Resource está entre parênteses. Habilite esse recurso do lado da sub-rede ao configurar pontos de extremidade de serviço para seu serviço:

**Disponível para o público geral**

- **[Armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): geralmente disponível em todas as regiões do Azure.
- **[Banco de dados SQL do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em todas as regiões do Azure.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em todas as regiões do Azure para pools de SQL dedicados (anteriormente conhecido como SQL DW).
- **[Banco de dados do Azure para servidor PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Banco de dados do Azure para servidor MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Banco de dados do Azure para MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): geralmente disponível em todas as regiões do Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft. keyvault*): geralmente disponível em todas as regiões do Azure.
- **[Barramento de serviço do Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): geralmente disponível em todas as regiões do Azure.
- **[Hubs de eventos do Azure](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): geralmente disponíveis em todas as regiões do Azure.
- **[Azure data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): geralmente disponível em todas as regiões do Azure em que ADLS Gen1 está disponível.
- **[Serviço de Azure app](../app-service/app-service-ip-restrictions.md)** (*Microsoft. Web*): geralmente disponível em todas as regiões do Azure em que o serviço de aplicativo está disponível.
- **[Serviços cognitivas do Azure](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft. cognitivaservices*): geralmente disponíveis em todas as regiões do Azure em que os serviços cognitivas estão disponíveis.

**Versão prévia pública**

- **[Registro de contêiner do Azure](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): visualização disponível em regiões do Azure limitadas em que o registro de contêiner do Azure está disponível.

Para obter as notificações mais atualizadas, verifique a página [atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) .

## <a name="key-benefits"></a>Principais benefícios

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para seus recursos de serviço do Azure**: espaços de endereço privado de VNet podem se sobrepor. Não é possível usar espaços sobrepostos para identificar exclusivamente o tráfego originado de sua VNet. Os pontos de extremidade de serviço fornecem a capacidade de proteger os recursos de serviço do Azure para sua rede virtual, estendendo a identidade de VNet para o serviço. Depois de habilitar os pontos de extremidade de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure para sua rede virtual. A adição de regra fornece segurança aprimorada removendo totalmente o acesso à Internet pública aos recursos e permitindo o tráfego somente de sua rede virtual.
- **Roteamento ideal para o tráfego de serviço do Azure de sua rede virtual**: hoje, todas as rotas em sua rede virtual que forçam o tráfego de Internet para seus dispositivos locais e/ou virtuais também forçam o tráfego de serviço do Azure a obter a mesma rota que o tráfego de Internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure. 

  Os pontos de extremidade sempre usam o tráfego do serviço diretamente da sua rede virtual para o serviço na rede de backbone do Microsoft Azure. Manter o tráfego na rede do backbone do Azure permite que você continue auditando e monitorando o tráfego da Internet de saída das suas redes virtuais, por meio de túnel forçado, sem afetar o tráfego do serviço. Para obter mais informações sobre rotas definidas pelo usuário e túnel forçado, consulte [Roteamento de tráfego de rede virtual do Azure](virtual-networks-udr-overview.md).
- **Simples de configurar com menos sobrecarga de gerenciamento**: você não precisa mais de endereços IP públicos reservados em suas redes virtuais para proteger recursos do Azure através do firewall de IP. Não há nenhum NAT (conversão de endereços de rede) ou dispositivos de gateway necessários para configurar os pontos de extremidade de serviço. Você pode configurar pontos de extremidade de serviço por meio de um simples clique em uma sub-rede. Não há nenhuma sobrecarga adicional para manter os pontos de extremidade.

## <a name="limitations"></a>Limitações

- O recurso está disponível apenas para redes virtuais implantadas usando o modelo de implantação do Azure Resource Manager.
- Os pontos de extremidade são habilitados nas sub-redes configuradas em redes virtuais do Azure. Os pontos de extremidade não podem ser usados para o tráfego de seu local para os serviços do Azure. Para obter mais informações, consulte [proteger o acesso do serviço do Azure do local](#secure-azure-services-to-virtual-networks)
- Para o SQL do Azure, um ponto de extremidade de serviço aplica-se somente ao tráfego de serviço do Azure dentro de uma região da rede virtual. Para o armazenamento do Azure, os pontos de extremidade também se estendem para incluir regiões emparelhadas em que você implanta a rede virtual para dar suporte ao tráfego de Read-Access Geo-Redundant armazenamento (RA-GRS) e ao armazenamento de Geo-Redundant (GRS). Para obter mais informações, consulte [Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Para Azure Data Lake Storage (ADLS) Gen 1, o recurso de integração VNet só está disponível para redes virtuais na mesma região. Observe também que a integração de rede virtual para ADLS Gen1 usa a segurança do ponto de extremidade do serviço de rede virtual entre sua rede virtual e Azure Active Directory (AD do Azure) para gerar declarações de segurança adicionais no token de acesso. Essas declarações, em seguida, são usadas para autenticar sua rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. A marca *Microsoft. AzureActiveDirectory* listada em serviços que dão suporte a pontos de extremidade de serviço é usada somente para dar suporte a pontos de extremidade de serviço para o ADLS Gen 1. O Azure AD não dá suporte a pontos de extremidade de serviço nativamente. Para obter mais informações sobre a integração de VNet do Azure Data Lake Store Gen 1, consulte [segurança de rede em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Proteger serviços do Azure para redes virtuais

- Um ponto de extremidade de serviço de rede virtual fornece a identidade da sua rede virtual para o serviço do Azure. Depois de habilitar os pontos de extremidade de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure para sua rede virtual.
- Atualmente, o tráfego do serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem. No caso dos pontos de extremidade de serviço, o tráfego do serviço muda para o uso de endereços de rede virtual privados como endereços IP de origem na hora de acessar o serviço do Azure de uma rede virtual. Essa opção permite que você acesse os serviços sem a necessidade dos endereços IP públicos reservados usados nos firewalls de IP.

   >[!NOTE]
   > Com pontos de extremidade de serviço, os endereços IP de origem das máquinas virtuais na sub-rede de tráfego do serviço troca de usar endereços IPv4 públicos para usar endereços IPv4 privados. As regras de firewall existentes do serviço do Azure usando endereços IP públicos do Azure irão parar de funcionar com essa troca. Certifique-se de que as regras de firewall de serviço do Azure permitem essa troca antes de configurar os pontos de extremidade de serviço. Você também pode enfrentar interrupções temporárias no tráfego de serviço dessa sub-rede ao configurar pontos de extremidade de serviço. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Proteger o acesso do serviço do Azure do local

  Por padrão, os recursos de serviço do Azure protegidos para redes virtuais não são acessíveis de redes locais. Se você quiser permitir o tráfego do local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou ExpressRoute. Você pode adicionar esses endereços IP por meio da configuração de firewall IP para recursos de serviço do Azure.

  ExpressRoute: se você estiver usando o [expressroute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para emparelhamento público ou emparelhamento da Microsoft de seu local, será necessário identificar os endereços IP de NAT que você está usando. Para o emparelhamento público, cada circuito do ExpressRoute usa dois endereços IP NAT, por padrão, aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT são fornecidos pelo cliente ou fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Para obter mais informações sobre NAT para emparelhamento público e da Microsoft para o ExpressRoute, consulte [requisitos de NAT do expressroute](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Proteção dos serviços do Azure em redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Configurar pontos de extremidade de serviço em uma sub-rede em uma rede virtual. Os pontos de extremidade funcionam com qualquer tipo de instâncias de computação em execução dentro dessa sub-rede.
- Você pode configurar vários pontos de extremidade de serviço para todos os serviços do Azure com suporte (armazenamento do Azure ou banco de dados SQL do Azure, por exemplo) em uma sub-rede.
- Para o Banco de Dados SQL do Azure, as redes virtuais devem estar na mesma região do recurso do serviço do Azure. Se estiver usando contas GRS e RA-GRS do Armazenamento do Azure, a conta primária deverá estar na mesma região que a rede virtual. Para todos os outros serviços, você pode proteger os recursos de serviço do Azure para redes virtuais em qualquer região. 
- A rede virtual em que o ponto de extremidade está configurado pode estar na mesma assinatura que o recurso do serviço do Azure ou em assinatura diferente. Para obter mais informações sobre as permissões necessárias para configurar pontos de extremidade e garantir os serviços do Azure, confira [Provisionamento](#provisioning).
- Para os serviços compatíveis, você pode proteger recursos novos ou existentes em redes virtuais usando pontos de extremidade de serviço.

### <a name="considerations"></a>Considerações

- Depois de habilitar um ponto de extremidade de serviço, os endereços IP de origem são alternados do uso de endereços IPv4 públicos para usar seu endereço IPv4 privado ao se comunicar com o serviço dessa sub-rede. Eventuais conexões TCP abertas existentes para o serviço são fechadas durante essa mudança. Verifique se não há tarefas críticas em execução ao habilitar ou desabilitar um ponto de extremidade de serviço para um serviço que atende a uma sub-rede. Além disso, verifique se seus aplicativos podem se conectar automaticamente aos serviços do Azure após a mudança de endereço IP.

  A mudança de endereço IP só afeta o tráfego do serviço de sua rede virtual. Não há nenhum impacto em qualquer outro tráfego endereçado de ou para os endereços IPv4 públicos atribuídos às suas máquinas virtuais. No caso dos serviços do Azure, se você tem regras de firewall existentes usando endereços IP públicos do Azure, essas regras param de funcionar com a mudança para endereços de rede virtual privados.
- Com os pontos de extremidade de serviço, as entradas DNS dos serviços do Azure permanecem como estão hoje e continuam a ser resolvidas para endereços IP públicos atribuídos ao serviço do Azure.

- NSGs (grupos de segurança de rede) com pontos de extremidade de serviço:
  - Por padrão, o NSGs permite o tráfego de Internet de saída e também permite o tráfego de sua VNet para os serviços do Azure. Esse tráfego continua a funcionar com pontos de extremidade de serviço como está. 
  - Se você quiser negar todo o tráfego de Internet de saída e permitir apenas o tráfego para serviços específicos do Azure, poderá fazer isso usando as [marcas de serviço](./network-security-groups-overview.md#service-tags) em seu NSGs. Você pode especificar os serviços do Azure com suporte como destino em suas regras do NSG e o Azure também fornece a manutenção de endereços IP subjacentes a cada marca. Para saber mais, confira [Marcas do Serviço do Azure para NSGs.](./network-security-groups-overview.md#service-tags) 

### <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: para proteger os serviços do Azure em várias sub-redes em uma rede virtual ou em várias redes virtuais, você pode habilitar pontos de extremidade de serviço em cada uma das sub-redes independentemente e proteger os recursos do serviço do Azure em todas elas.
- **Filtrando o tráfego de saída de uma rede virtual para os serviços do Azure**: se você quiser inspecionar ou filtrar o tráfego enviado a um serviço do Azure de uma rede virtual, poderá implantar um dispositivo de rede virtual na rede virtual. Você pode aplicar os pontos de extremidade de serviço à sub-rede na qual o dispositivo de rede virtual está implantado e proteger os recursos do serviço do Azure apenas nessa sub-rede. Esse cenário pode ser útil se você quiser usar a filtragem de dispositivo de virtualização de rede para restringir o acesso de serviço do Azure de sua rede virtual somente a recursos específicos do Azure. Para obter mais informações, consulte [Saída com dispositivos de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Protegendo os recursos do Azure para serviços implantados diretamente em redes virtuais**: você pode implantar diretamente vários serviços do Azure em sub-redes específicas em uma rede virtual. Você pode proteger recursos do serviço do Azure em sub-redes de [serviço gerenciado](virtual-network-for-azure-services.md) configurando um ponto de extremidade de serviço na sub-rede de serviço gerenciado.
- **Tráfego de disco de uma máquina virtual do Azure: o** tráfego de disco da máquina virtual para discos gerenciados e não gerenciados não é afetado por pontos de extremidade de serviço que encaminham alterações para o armazenamento do Azure. Esse tráfego inclui diskIO, bem como montagem e desmontagem. Você pode limitar o acesso REST a blobs de página para selecionar redes por meio de pontos de extremidade de serviço e [regras de rede de armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas

Depois de configurar os pontos de extremidade de serviço para um serviço específico, valide se a rota do ponto final do serviço está em vigor por: 
 
- Validação do endereço IP de origem de qualquer solicitação de serviço no diagnóstico de serviço. Todas as novas solicitações com pontos de extremidade de serviço mostram o endereço IP de origem para a solicitação como o endereço IP privado de rede virtual atribuído ao cliente que fez a solicitação da sua rede virtual. Sem o ponto de extremidade, esse endereço é um endereço IP público do Azure.
- Exibindo as rotas efetivas em qualquer adaptador de rede em uma sub-rede. A rota para o serviço:
  - Mostra uma rota padrão mais específica para intervalos de prefixo de endereço de cada serviço
  - Tem um nextHopType como *VirtualNetworkServiceEndpoint*
  - Indica que uma conexão mais direta com o serviço está em vigor em comparação com as rotas de túnel forçado

>[!NOTE]
> Rotas de ponto de extremidade de serviço substituem quaisquer rotas BGP ou UDR na correspondência do prefixo de endereço de um serviço do Azure. Para obter mais informações, consulte [solução de problemas com rotas efetivas](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Provisionamento

Os pontos de extremidade de serviço podem ser configurados em redes virtuais independentemente de um usuário com acesso de gravação a uma rede virtual. Para proteger os recursos de serviço do Azure para uma VNet, o usuário deve ter permissão para *Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action* para as sub-redes adicionadas. As funções internas de administrador de serviços incluem essa permissão por padrão. Você pode modificar a permissão criando funções personalizadas.

Para obter mais informações sobre funções internas, consulte [funções internas do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obter mais informações sobre como atribuir permissões específicas a funções personalizadas, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Determinados serviços do Azure (nem todos), como o armazenamento do Azure e Azure Key Vault também oferecem suporte a pontos de extremidade de serviço em locatários diferentes de Active Directory (AD), ou seja, a rede virtual e o recurso de serviço do Azure podem estar em locatários diferentes de Active Directory (AD). Consulte a documentação do serviço individual para obter mais detalhes.  

## <a name="pricing-and-limits"></a>Preços e limites

Não há nenhum custo adicional para usar pontos de extremidade de serviço. O modelo de preços atual para os serviços do Azure (armazenamento do Azure, banco de dados SQL do Azure, etc.) aplica-se como está hoje.

Não há limite para o número total de pontos de extremidade de serviço em uma rede virtual.

Determinados serviços do Azure, como contas de armazenamento do Azure, podem impor limites no número de sub-redes usadas para proteger o recurso. Consulte a documentação para vários serviços na seção [próximas etapas](#next-steps) para obter detalhes.

## <a name="vnet-service-endpoint-policies"></a>Políticas de ponto de extremidade de serviço VNet 

As políticas de ponto de extremidade de serviço de VNet permitem filtrar o tráfego de rede virtual para os serviços do Azure. Esse filtro permite apenas recursos específicos do serviço do Azure em pontos de extremidade de serviço. As políticas de ponto de extremidade de serviço fornecem controle de acesso granular para tráfego de rede virtual para serviços do Azure. Para obter mais informações, consulte [políticas de ponto de extremidade de serviço de rede virtual](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>Perguntas frequentes

Para perguntas frequentes, consulte [ponto de extremidade de serviço de rede virtual FAQs](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Próximas etapas

- [Configurar pontos de extremidade de serviço de rede virtual](tutorial-restrict-network-access-to-resources.md)
- [Proteger uma conta de armazenamento do Azure para uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteger um banco de dados SQL do Azure para uma rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteger uma análise de Synapse do Azure para uma rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integração de serviço do Azure em redes virtuais](virtual-network-for-azure-services.md)
- [Políticas de Ponto de Extremidade de Serviço de Rede Virtual](./virtual-network-service-endpoint-policies-overview.md)
- [Modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
