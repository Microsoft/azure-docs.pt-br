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
ms.date: 08/15/2018
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: e36e1ca17b5106c79076d1c62e737ba60907ab19
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666466"
---
# <a name="virtual-network-service-endpoints"></a>Pontos de extremidade de serviço de rede virtual

Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Este recurso está disponível para os seguintes serviços e regiões do Azure:

**Disponível**

- **[Armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** : Disponível em todas as regiões do Azure.
- **[Banco de Dados SQL do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure.
- **[SQL Data Warehouse do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure.
- **[Servidor do Banco de Dados do Azure para PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível ao público em geral em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Servidor do Banco de Dados do Azure para MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível ao público em geral em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Banco de Dados do Azure para MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** : Disponível ao público em geral em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** : Disponível em todas as regiões do Azure.
- **[Barramento de Serviço do Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure.
- **[Hubs de Eventos do Azure](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure.
- **[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** : Disponível em todas as regiões do Azure nas quais o ADLS Gen1 está disponível.
- **[O serviço de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Disponível em todas as regiões do Azure em que o serviço de aplicativo está disponível

**Versão prévia pública**

- **[Registro de contêiner do Azure](../container-registry/container-registry-vnet.md)** : Visualização disponível em todas as regiões do Azure em que o registro de contêiner do Azure está disponível.
.

Para obter as notificações mais recentes, verifique a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Principais benefícios

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para os recursos do serviço do Azure**: O espaço de endereço privado da rede virtual pode estar sobreposto e, portanto, não pode ser usado para identificar de modo exclusivo o tráfego originado da sua rede virtual. Pontos de extremidade de serviço fornecem a capacidade de proteger os recursos de serviço do Azure para sua rede virtual, estendendo a identidade de rede virtual ao serviço. Depois que os pontos de extremidade de serviço são habilitados na sua rede virtual, você pode proteger os recursos de serviço do Azure em sua rede virtual adicionando uma regra da rede virtual para os recursos. Isso proporciona melhor segurança, removendo totalmente o acesso público à Internet dos recursos e permitindo o tráfego somente da sua rede virtual.
- **Roteamento de tráfego ideal para o serviço do Azure de sua rede virtual**: Atualmente, todas as rotas em sua rede virtual que forçam o tráfego de Internet para dispositivos locais e/ou virtuais, conhecido como túnel forçado, também forçam o tráfego do serviço do Azure para seguir a mesma rota que o tráfego de Internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure. 

  Os pontos de extremidade sempre usam o tráfego do serviço diretamente da sua rede virtual para o serviço na rede de backbone do Microsoft Azure. Manter o tráfego na rede do backbone do Azure permite que você continue auditando e monitorando o tráfego da Internet de saída das suas redes virtuais, por meio de túnel forçado, sem afetar o tráfego do serviço. Saiba mais sobre [rotas definidas pelo usuário e túnel forçado](virtual-networks-udr-overview.md).
- **Simples de configurar com menos sobrecarga de gerenciamento**: Você não precisa mais de endereços IP públicos reservados nas suas redes virtuais para garantir recursos do Azure pelo firewall do IP. Não é obrigatório nenhum dispositivo NAT ou de gateway para configurar os pontos de extremidade de serviço. Pontos de extremidade de serviço são configurados por meio de um clique simples em uma sub-rede. Não há sobrecarga adicional para manter os pontos de extremidade.

## <a name="limitations"></a>Limitações

- O recurso está disponível apenas para redes virtuais implantadas usando o modelo de implantação do Azure Resource Manager.
- Os pontos de extremidade são habilitados nas sub-redes configuradas em redes virtuais do Azure. Os pontos de extremidade não podem ser usados para tráfego do seu local para os serviços do Azure. Para obter mais informações, consulte ["Garantindo o acesso local ao serviço do Azure"](#securing-azure-services-to-virtual-networks)
- Para o SQL do Azure, um ponto de extremidade de serviço aplica-se somente ao tráfego de serviço do Azure dentro de uma região da rede virtual. Para o Armazenamento do Microsoft Azure, para dar suporte a tráfego GRS e RA-GRS, pontos de extremidade também incluem as regiões emparelhadas em que a rede virtual é implantada. Saiba mais sobre as [regiões emparelhadas do Azure.](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Para o ADLS Gen 1, a funcionalidade Integração VNet só está disponível para as redes virtuais na mesma região. Também Observe que torna a integração de rede virtual para o Azure Data Lake armazenamento Gen1 usa a segurança de ponto de extremidade de serviço de rede virtual entre sua rede virtual e o Azure Active Directory (Azure AD) para gerar declarações de segurança adicional no token de acesso. Essas declarações, em seguida, são usadas para autenticar sua rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Marca de "Microsoft. azureactivedirectory" listada em serviços que dão suporte a pontos de extremidade de serviço é usada apenas para dar suporte a pontos de extremidade de serviço para o ADLS Gen 1. Azure Active Directory (AD do Azure) não dá suporte a pontos de extremidade de serviço nativamente. Saiba mais sobre [integração de rede virtual do Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="securing-azure-services-to-virtual-networks"></a>Proteção dos serviços do Azure em redes virtuais

- Um ponto de extremidade de serviço de rede virtual fornece a identidade da sua rede virtual para o serviço do Azure. Depois que os pontos de extremidade de serviço são habilitados na sua rede virtual, você pode proteger os recursos de serviço do Azure em sua rede virtual adicionando uma regra da rede virtual para os recursos.
- Atualmente, o tráfego do serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem. No caso dos pontos de extremidade de serviço, o tráfego do serviço muda para o uso de endereços de rede virtual privados como endereços IP de origem na hora de acessar o serviço do Azure de uma rede virtual. Essa opção permite que você acesse os serviços sem a necessidade dos endereços IP públicos reservados usados nos firewalls de IP.

>[!NOTE]
> Com pontos de extremidade de serviço, os endereços IP de origem das máquinas virtuais na sub-rede de tráfego do serviço troca de usar endereços IPv4 públicos para usar endereços IPv4 privados. As regras de firewall existentes do serviço do Azure usando endereços IP públicos do Azure irão parar de funcionar com essa troca. Certifique-se de que as regras de firewall de serviço do Azure permitem essa troca antes de configurar os pontos de extremidade de serviço. Você também pode enfrentar interrupções temporárias no tráfego de serviço dessa sub-rede ao configurar pontos de extremidade de serviço. 
 
- __Garantindo o acesso do serviço do Azure localmente__:

  Por padrão, os recursos do serviço do Azure garantidos para redes virtuais não podem ser acessados nas redes locais. Se você quiser permitir o tráfego vindo do local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou ExpressRoute. Esses endereços IP podem ser adicionados por meio da configuração de firewall de IP dos recursos de serviço do Azure.

  ExpressRoute: Se você estiver usando [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviço. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP públicos do circuito do ExpressRoute de emparelhamento, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Proteção dos serviços do Azure em redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Os pontos de extremidade de serviço são configurados em uma sub-rede em uma rede virtual. Os pontos de extremidade funcionam com qualquer tipo de instâncias de computação em execução dentro dessa sub-rede.
- Você pode configurar vários pontos de extremidade de serviço para todos os serviços do Azure compatíveis (por exemplo, Armazenamento do Azure ou Banco de dados SQL do Azure) em uma sub-rede.
- Para o Banco de Dados SQL do Azure, as redes virtuais devem estar na mesma região do recurso do serviço do Azure. Se estiver usando contas GRS e RA-GRS do Armazenamento do Azure, a conta primária deverá estar na mesma região que a rede virtual. Para todos os outros serviços, recursos de serviço do Azure podem ser protegidos para redes virtuais em qualquer região. 
- A rede virtual em que o ponto de extremidade está configurado pode estar na mesma assinatura que o recurso do serviço do Azure ou em assinatura diferente. Para obter mais informações sobre as permissões necessárias para configurar pontos de extremidade e garantir os serviços do Azure, confira [Provisionamento](#provisioning).
- Para os serviços compatíveis, você pode proteger recursos novos ou existentes em redes virtuais usando pontos de extremidade de serviço.

### <a name="considerations"></a>Considerações

- Após habilitar um ponto de extremidade de serviço, os endereços IP de origem das máquinas virtuais na sub-rede mudam do uso de endereços IPv4 públicos para o uso dos respectivos endereços IPv4 privados na hora de se comunicar com o serviço dessa sub-rede. Eventuais conexões TCP abertas existentes para o serviço são fechadas durante essa mudança. Verifique se não há tarefas críticas em execução ao habilitar ou desabilitar um ponto de extremidade de serviço para um serviço que atende a uma sub-rede. Além disso, verifique se seus aplicativos podem se conectar automaticamente aos serviços do Azure após a mudança de endereço IP.

  A mudança de endereço IP só afeta o tráfego do serviço de sua rede virtual. Não há nenhum impacto a nenhum outro tráfego de ou para endereços IPv4 públicos atribuídos às máquinas virtuais. No caso dos serviços do Azure, se você tem regras de firewall existentes usando endereços IP públicos do Azure, essas regras param de funcionar com a mudança para endereços de rede virtual privados.
- Com pontos de extremidade de serviço, as entradas DNS para os serviços do Azure permanecem como são atualmente e continuam a ser resolvidas para endereços IP públicos atribuídos ao serviço do Azure.

- NSGs (grupos de segurança de rede) com pontos de extremidade de serviço:
  - Por padrão, os NSGs permitem tráfego de Internet de saída e, portanto, também permitem o tráfego da sua rede virtual para os serviços do Azure. Isso continuará a funcionar dessa forma, com pontos de extremidade de serviço. 
  - Se você deseja negar todo o tráfego da Internet de saída e permitir somente tráfego para serviços específicos do Azure, pode fazer isso usando [marcas de serviço](security-overview.md#service-tags) em seus NSGs. Você pode especificar os serviços do Azure com suporte como destino em suas regras NSG, e a manutenção de endereços IP subjacentes a cada marca é feita pelo Azure. Para saber mais, confira [Marcas do Serviço do Azure para NSGs.](security-overview.md#service-tags) 

### <a name="scenarios"></a>Cenários

- **Redes emparelhadas, conectadas ou múltiplas**: Para proteger os serviços do Azure em várias sub-redes em uma rede virtual ou em várias redes virtuais, você pode habilitar pontos de extremidade de serviço em cada uma das sub-redes independentemente e proteger os recursos do serviço do Azure em todas elas.
- **Filtrando tráfego de saída da rede virtual para os serviços do Azure**: Se deseja verificar ou filtrar o tráfego destinado a um serviço do Azure por meio de uma rede virtual, você pode implantar um dispositivo de rede virtual na rede virtual. Você pode aplicar os pontos de extremidade de serviço à sub-rede na qual o dispositivo de rede virtual está implantado e proteger os recursos do serviço do Azure apenas nessa sub-rede. Esse cenário pode ser útil se você deseja restringir o acesso do serviço do Azure de sua rede virtual somente para recursos específicos do Azure usando a filtragem por dispositivo de rede virtual. Para obter mais informações, consulte [Saída com dispositivos de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Garantindo os recursos do Azure para serviços implantados diretamente em redes virtuais**: Vários serviços do Azure podem ser implantados diretamente em sub-redes específicas em uma rede virtual. Você pode proteger recursos do serviço do Azure em sub-redes de [serviço gerenciado](virtual-network-for-azure-services.md) configurando um ponto de extremidade de serviço na sub-rede de serviço gerenciado.
- **Tráfego de disco de uma máquina virtual do Azure**: O tráfego de disco da máquina virtual (incluindo montagem, desmontagem, diskIO) para discos gerenciados/não gerenciados, não é afetado por pontos de extremidade do serviço que estão encaminhando alterações para o Armazenamento do Azure. Você pode limitar o acesso REST a blobs de página para selecionar as redes, por meio de pontos de extremidade de serviço e [regras de rede do Armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas

Quando os pontos de extremidade de serviço são configurados para um serviço específico, valide que a rota de ponto de extremidade de serviço está em vigor: 
 
- Validação do endereço IP de origem de qualquer solicitação de serviço no diagnóstico de serviço. Todas as novas solicitações com pontos de extremidade de serviço mostram o endereço IP de origem para a solicitação como o endereço IP privado de rede virtual atribuído ao cliente que fez a solicitação da sua rede virtual. Sem o ponto de extremidade, esse endereço é um endereço IP público do Azure.
- Exibindo as rotas efetivas em qualquer adaptador de rede em uma sub-rede. A rota para o serviço:
  - Mostra uma rota padrão mais específica para intervalos de prefixo de endereço de cada serviço
  - Tem um nextHopType como *VirtualNetworkServiceEndpoint*
  - Indica que uma conexão mais direta com o serviço está em vigor, comparada com rotas de túnel forçado

>[!NOTE]
> Rotas de ponto de extremidade de serviço substituem quaisquer rotas BGP ou UDR na correspondência do prefixo de endereço de um serviço do Azure. Saiba mais sobre [solução de problemas com rotas efetivas](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Provisionamento

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação a uma rede virtual. Para proteger recursos de serviço do Azure a uma rede virtual, o usuário deve ter permissão para *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* para as sub-redes que está sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço da rede virtual e do Azure estão em assinaturas diferentes, os recursos devem estar no mesmo locatário do Active Directory (AD). 

## <a name="pricing-and-limits"></a>Preços e limites

Não há nenhum custo adicional para usar pontos de extremidade de serviço. O modelo de preços atual para os serviços do Azure (Armazenamento do Microsoft Azure, Banco de Dados SQL do Azure etc.) se aplica como é atualmente.

Não há limite para o número total de pontos de extremidade de serviço em uma rede virtual.

Determinados serviços do Azure, como contas de armazenamento do Azure, podem impor limites no número de sub-redes usadas para garantir o recurso. Consulte a documentação para serviços diversos em [Próximas etapas](#next-steps) para obter detalhes.

## <a name="virtual-network-service-endpoint-policies"></a>Políticas de Ponto de Extremidade de Serviço de Rede Virtual 

As políticas de ponto de extremidade de serviço de rede virtual permitem filtrar o tráfego da rede virtual para serviços do Azure, permitindo apenas recursos específicos de serviço do Azure, em pontos de extremidade de serviço. As políticas de ponto de extremidade de serviço fornecem controle de acesso granular para tráfego de rede virtual para serviços do Azure. Mais informações: [Políticas de Ponto de Extremidade de Serviço de Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Perguntas frequentes

Para perguntas frequentes, confira as [Perguntas frequentes sobre o ponto de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar pontos de extremidade de serviço de rede virtual](tutorial-restrict-network-access-to-resources.md)
- Saiba como [proteger uma conta de Armazenamento do Azure em uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Saiba como [proteger um Banco de Dados SQL do Azure em uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Saiba como [garantir um SQL Data Warehouse do Azure para uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- Saiba mais sobre a [integração do serviço do Azure em redes virtuais](virtual-network-for-azure-services.md)
- Saiba mais sobre [Políticas de ponto de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Início Rápido: [Modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) para configurar o ponto de extremidade de serviço em uma sub-rede de rede virtual e ligar a conta de Armazenamento do Azure a essa sub-rede.

