---
title: "Pontos de extremidade de serviço de rede virtual do Azure | Microsoft Docs"
description: "Saiba como habilitar o acesso direto aos recursos do Azure de uma rede virtual usando pontos de extremidade de serviço."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 0a0fe6f0e353e33cec80a9e06a61e772931cdea6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-service-endpoints-preview"></a>Pontos de extremidade de serviço de rede virtual (versão prévia)

Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Este recurso está disponível em versão prévia para os seguintes serviços e regiões do Azure:

- **Armazenamento do Azure**: WestCentralUS, WestUS2, EastUS, WestUS, AustraliaEast e AustraliaSouthEast
- **Banco de dados SQL do Azure**: WestCentralUS, WestUS2 e EastUS.

Para obter as notificações mais recentes sobre a versão prévia, verifique a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

>[!NOTE]
> Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Principais benefícios

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para os recursos do serviço do Azure**: com pontos de extremidade de serviço, os recursos do serviço do Azure podem ser protegidos em sua rede virtual. Garantir recursos de serviço em uma rede virtual proporciona melhor segurança, removendo totalmente o acesso público da Internet a esses recursos e permitindo o tráfego somente da sua rede virtual.
- **Roteamento ideal para tráfego de serviço do Azure da rede virtual**: atualmente, todas as rotas em sua rede virtual que forçam o tráfego de Internet para dispositivos locais e/ou virtuais, conhecido como túnel forçado, também forçam o tráfego do serviço do Azure para seguir a mesma rota que o tráfego de Internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure. 

  Os pontos de extremidade sempre usam o tráfego do serviço diretamente da sua rede virtual para o serviço na rede de backbone do Microsoft Azure. Manter o tráfego na rede do backbone do Azure permite que você continue auditando e monitorando o tráfego da Internet de saída das suas redes virtuais, por meio de túnel forçado, sem afetar o tráfego do serviço. Saiba mais sobre [rotas definidas pelo usuário e túnel forçado](virtual-networks-udr-overview.md).
- **Simples de configurar com menos sobrecarga de gerenciamento**: você não precisa mais de endereços IP públicos reservados em suas redes virtuais para proteger recursos do Azure através do firewall de IP. Não é obrigatório nenhum dispositivo NAT ou de gateway para configurar os pontos de extremidade de serviço. Pontos de extremidade de serviço são configurados por meio de um clique simples em uma sub-rede. Não há sobrecarga adicional para manter os pontos de extremidade.

## <a name="limitations"></a>Limitações

- O recurso está disponível apenas para redes virtuais implantadas usando o modelo de implantação do Azure Resource Manager.
- Os pontos de extremidade são habilitados nas sub-redes configuradas em redes virtuais do Azure. Os pontos de extremidade não podem ser usados para tráfego do seu local para os serviços do Azure. Para obter mais informações, consulte ["Garantindo o acesso local ao serviço do Azure"](#securing-azure-services-to-virtual-networks)
- O ponto de extremidade de serviço aplica-se somente ao tráfego de serviço do Azure dentro de uma região da rede virtual. Para dar suporte a tráfego GRS e RA-GRS para o Armazenamento do Azure, pontos de extremidade também incluem as regiões emparelhadas em que a rede virtual é implantada. Saiba mais sobre as [regiões emparelhadas do Azure.](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)

## <a name="securing-azure-services-to-virtual-networks"></a>Proteção dos serviços do Azure em redes virtuais

- Um ponto de extremidade de serviço de rede virtual fornece a identidade da sua rede virtual para o serviço do Azure. Depois que os pontos de extremidade de serviço são habilitados na sua rede virtual, você pode proteger os recursos de serviço do Azure em sua rede virtual adicionando uma regra de rede virtual para os recursos.
- Atualmente, o tráfego do serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem. No caso dos pontos de extremidade de serviço, o tráfego do serviço muda para o uso de endereços de rede virtual privados como endereços IP de origem na hora de acessar o serviço do Azure de uma rede virtual. Essa opção permite que você acesse os serviços sem a necessidade dos endereços IP públicos reservados usados nos firewalls de IP.
- __Garantindo o acesso do serviço do Azure localmente__:

  Por padrão, os recursos do serviço do Azure garantidos para redes virtuais não podem ser acessados nas redes locais. Se você quiser permitir o tráfego vindo do local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou ExpressRoute. Esses endereços IP podem ser adicionados por meio da configuração de firewall de IP dos recursos de serviço do Azure.

  ExpressRoute: se você estiver usando o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) localmente, para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para permitir o acesso aos recursos do serviço, você deve permitir esses dois endereços IP públicos na configuração do firewall de IP do recurso. Para localizar seus endereços IP do circuito de ExpressRoute, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Proteção dos serviços do Azure em redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Os pontos de extremidade de serviço são configurados em uma sub-rede em uma rede virtual. Os pontos de extremidade funcionam com qualquer tipo de instâncias de computação em execução dentro dessa sub-rede.
- Somente um ponto de extremidade de serviço pode ser habilitado para determinado serviço de uma sub-rede. Você pode configurar vários pontos de extremidade de serviço para todos os serviços do Azure compatíveis (por exemplo, Armazenamento do Azure ou Banco de dados SQL do Azure) em uma sub-rede.
- As redes virtuais devem estar na mesma região do recurso do serviço do Azure. Se estiver usando contas GRS e RA-GRS do Armazenamento do Azure, a conta primária deverá estar na mesma região que a rede virtual.
- A rede virtual em que o ponto de extremidade está configurado pode estar na mesma assinatura que o recurso do serviço do Azure ou em assinatura diferente. Para obter mais informações sobre as permissões necessárias para configurar pontos de extremidade e garantir os serviços do Azure, confira [Provisionamento](#Provisioning).
- Para os serviços compatíveis, você pode proteger recursos novos ou existentes em redes virtuais usando pontos de extremidade de serviço.

### <a name="considerations"></a>Considerações

- Após habilitar um ponto de extremidade de serviço, os endereços IP de origem das máquinas virtuais na sub-rede mudam do uso de endereços IPv4 públicos para o uso dos respectivos endereços IPv4 privados na hora de se comunicar com o serviço dessa sub-rede. Eventuais conexões TCP abertas existentes para o serviço são fechadas durante essa mudança. Verifique se não há tarefas críticas em execução ao habilitar ou desabilitar um ponto de extremidade de serviço para um serviço que atende a uma sub-rede. Além disso, verifique se seus aplicativos podem se conectar automaticamente aos serviços do Azure após a mudança de endereço IP.

  A mudança de endereço IP só afeta o tráfego do serviço de sua rede virtual. Não há nenhum impacto a nenhum outro tráfego de ou para endereços IPv4 públicos atribuídos às máquinas virtuais. No caso dos serviços do Azure, se você tem regras de firewall existentes usando endereços IP públicos do Azure, essas regras param de funcionar com a mudança para endereços de rede virtual privados.
- Com pontos de extremidade de serviço, as entradas DNS para os serviços do Azure permanecem como são atualmente e continuam a ser resolvidas para endereços IP públicos atribuídos ao serviço do Azure.
- NSGs (grupos de segurança de rede) com pontos de extremidade de serviço:
  - Por padrão, os NSGs permitem tráfego de Internet de saída e, portanto, também permitem o tráfego da sua rede virtual para os serviços do Azure. Isso continuará a funcionar dessa forma, com pontos de extremidade de serviço. 
  - Se você deseja negar todo o tráfego da Internet de saída e permitir somente tráfego para serviços específicos do Azure, pode fazer isso usando __"marcas de serviço do Azure"__ nos seus NSGs. Você pode especificar os serviços do Azure com suporte como destino em suas regras NSG, e a manutenção de endereços IP subjacentes a cada marca é feita pelo Azure. Para saber mais, confira [Marcas do Serviço do Azure para NSGs.](https://aka.ms/servicetags) 

### <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: para proteger os serviços do Azure em várias sub-redes em uma rede virtual ou em várias redes virtuais, você pode habilitar pontos de extremidade de serviço em cada uma das sub-redes independentemente e proteger os recursos do serviço do Azure em todas elas.
- **Filtragem de tráfego de saída de uma rede virtual para serviços do Azure**: se você deseja verificar ou filtrar o tráfego proveniente de uma rede virtual e destinado a um serviço do Azure, você pode implantar um dispositivo de rede virtual nessa rede virtual. Você pode aplicar os pontos de extremidade de serviço à sub-rede na qual o dispositivo de rede virtual está implantado e proteger os recursos do serviço do Azure apenas nessa sub-rede. Esse cenário pode ser útil se você deseja restringir o acesso do serviço do Azure de sua rede virtual somente para recursos específicos do Azure usando a filtragem por dispositivo de rede virtual. Para obter mais informações, consulte [Saída com dispositivos de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Garantindo recursos do Azure para serviços implantados diretamente em redes virtuais**: vários serviços do Azure podem ser implantados diretamente em sub-redes específicas em uma rede virtual. Você pode proteger recursos do serviço do Azure em sub-redes de [serviço gerenciado](virtual-network-for-azure-services.md) configurando um ponto de extremidade de serviço na sub-rede de serviço gerenciado.

### <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas

Quando os pontos de extremidade de serviço são configurados para um serviço específico, valide que a rota de ponto de extremidade de serviço está em vigor: 
 
- Validação do endereço IP de origem de qualquer solicitação de serviço no diagnóstico de serviço. Todas as novas solicitações com pontos de extremidade de serviço mostram o endereço IP de origem para a solicitação como o endereço IP privado de rede virtual atribuído ao cliente que fez a solicitação da sua rede virtual. Sem o ponto de extremidade, esse endereço é um endereço IP público do Azure.
- Exibindo as rotas efetivas em qualquer adaptador de rede em uma sub-rede. A rota para o serviço:
  - Mostra uma rota padrão mais específica para intervalos de prefixo de endereço de cada serviço
  - Tem um nextHopType como *VirtualNetworkServiceEndpoint*
  - Indica que uma conexão mais direta com o serviço está em vigor, comparada com rotas de túnel forçado

>[!NOTE]
> Rotas de ponto de extremidade de serviço substituem quaisquer rotas BGP ou UDR na correspondência do prefixo de endereço de um serviço do Azure. Saiba mais sobre [solução de problemas com rotas efetivas](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>Provisionamento

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação a uma rede virtual. Para proteger recursos de serviço do Azure em uma rede virtual, o usuário deve ter a permissão de *Microsoft.Network/JoinServicetoaSubnet* para as sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço da rede virtual e do Azure estão em assinaturas diferentes, os recursos devem estar no mesmo locatário do AD (Active Directory) durante a versão prévia. 

## <a name="pricing-and-limits"></a>Preços e limites

Não há nenhum custo adicional para usar pontos de extremidade de serviço. O modelo de preços atual para os serviços do Azure (Armazenamento do Azure, Banco de Dados SQL do Azure) se aplica como é atualmente.

Não há limite para o número total de pontos de extremidade de serviço em uma rede virtual.

Para um recurso de serviço do Azure (por exemplo, uma conta de Armazenamento do Azure), os serviços podem impor limites para o número de sub-redes usadas para garantir o recurso. Consulte a documentação para serviços diversos em [Próximas etapas](#next-steps) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-configure.md)
- Saiba como [proteger uma conta de Armazenamento do Azure em uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Saiba como [proteger um Banco de Dados SQL do Azure em uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Saiba mais sobre a [integração do serviço do Azure em redes virtuais](virtual-network-for-azure-services.md)

