---
title: Rede virtual dos serviços do Azure
titlesuffix: Azure Virtual Network
description: Saiba como implantar serviços dedicados do Azure em uma rede virtual e saiba mais sobre os recursos que essas implantações fornecem.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 79b6fa1043b1bb8add6b6beb5fd38312ee12ea75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210267"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Implantar serviços dedicados do Azure em redes virtuais

Quando você implanta serviços dedicados do Azure em uma [rede virtual](virtual-networks-overview.md), é possível comunicar-se com os recursos de serviço de modo privado, por meio de endereços IP privados.

![Serviços implantados em uma rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implantar serviços em uma rede virtual fornece as seguintes funcionalidades:

- Recursos dentro da rede virtual podem se comunicar uns com os outros de modo privado, por meio de endereços IP privados. Por exemplo, transferindo dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na rede virtual.
- Recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados por um [VPN Site a Site (Gateway de VPN)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem ser [emparelhadas](virtual-network-peering-overview.md) para permitir que recursos nas redes virtuais se comuniquem entre si usando endereços IP privados.
- As instâncias de serviço em uma rede virtual normalmente são totalmente gerenciadas pelo serviço do Azure. Isso inclui o monitoramento da integridade dos recursos e o dimensionamento com carga.
- Instâncias de serviço são implantadas em uma sub-rede em uma rede virtual. O acesso de rede de entrada e de saída para a sub-rede deve ser aberto por meio de [grupos de segurança de rede](./network-security-groups-overview.md#network-security-groups), segundo as diretrizes fornecidas pelos serviços.
- Determinados serviços também impõem restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço na mesma sub-rede. Verifique as restrições específicas de cada serviço, pois elas podem mudar ao longo do tempo. Exemplos desses serviços são Azure NetApp Files, HSM Dedicado, Instâncias de Contêiner do Azure, Serviço de Aplicativo. 
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito de que uma sub-rede pode hospedar um serviço específico. Ao delegar, os serviços obtêm permissões explícitas para a criação de recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta da API REST em uma [rede virtual com uma sub-rede delegada](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Uma lista abrangente de serviços que estão usando o modelo de sub-rede delegada pode ser obtida da API [Delegações Disponíveis](/rest/api/virtualnetwork/availabledelegations/list).

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implantados em uma rede virtual

|Categoria|Serviço| Sub-rede dedicada<sup>1</sup>
|-|-|-|
| Computação | Máquinas virtuais: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço de Nuvem](/previous-versions/azure/reference/jj156091(v=azure.100)): Rede virtual (Clássico) apenas<br/> [Lote do Azure](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> Não<sup>2</sup>
| Rede | [Gateway de Aplicativo – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Soluções de virtualização de rede](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Sim <br/> Sim <br/> Sim <br/> Sim <br/> Não
|Dados|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não<sup>2</sup> <br/> Não<sup>2</sup> <br/> 
| Identidade | [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contêineres | [AKS (Serviço de Kubernetes do Azure)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[ACI (Instância de Contêiner do Azure)](https://www.aka.ms/acivnet)<br/>[Mecanismo do Serviço de Contêiner do Azure](https://github.com/Azure/acs-engine) com o [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI de Rede Virtual do Microsoft Azure<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Não<sup>2</sup><br/> Sim <br/> Não <br/> Sim
| Web | [Gerenciamento da API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicativos Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicativo](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim <br/> Sim
| Hospedado | [HSM Dedicado do Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| Azure Spring Cloud | [Implantar na rede virtual do Azure (injeção de VNet)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Sim <br/>
| | |

<sup>1</sup> "Dedicada" implica que apenas recursos específicos de serviço podem ser implantados nesta sub-rede e não podem ser combinados com as VM/VMSSs do cliente <br/> 
<sup>2</sup> É recomendável como uma melhor prática ter esses serviços em uma sub-rede dedicada, mas esse não é um requisito obrigatório imposto pelo serviço.
