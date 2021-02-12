---
title: Gerenciar endereços IP públicos | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Gerenciar endereços IP públicos.  Além disso, saiba como um endereço IP público é um recurso com suas próprias configurações configuráveis.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934947"
---
# <a name="manage-public-ip-addresses"></a>Gerenciar endereços IP públicos

Saiba mais sobre um endereço IP público e como criá-lo, alterá-lo e excluí-lo. Um endereço IP público é um recurso com suas próprias definições configuráveis. A atribuição de um endereço IP público a um recurso do Azure que dá suporte a endereços IP públicos permite:
- A comunicação de entrada da Internet com o recurso, como VMs (Máquinas Virtuais) do Azure, Gateways de Aplicativo do Azure, Balanceadores de Carga do Azure, Gateways de VPN do Azure e outros. Você ainda pode se comunicar com alguns recursos, como VMs, da Internet, se uma VM não tem um endereço IP público atribuído a ela, desde que a VM faça parte de um pool de back-end do balanceador de carga e o balanceador de carga receba um endereço IP público. Para determinar se um recurso de um serviço específico do Azure pode receber um endereço IP público, ou se ele pode ser comunicado por meio do endereço IP público de outro recurso do Azure, confira a documentação do serviço.
- Conectividade de saída à Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode se comunicar pela saída com a Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede convertido pelo Azure em um endereço público imprevisível, por padrão. Atribuir um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para saber mais, confira [Criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre as conexões de saída nos recursos do Azure, confira [Entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

Os endereços IP públicos têm um encargo nominal. Para ver os preços, acesse a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para obter instruções sobre como criar endereços IP públicos usando o portal, o PowerShell ou a CLI –, consulte as seguintes páginas:

 * [Criar endereços IP públicos – portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Criar endereços IP públicos – PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Criar endereços IP públicos – CLI do Azure](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Embora o portal forneça a opção de criar dois recursos de endereço IP público (um IPv4 e um IPv6), os comandos do PowerShell e da CLI criam um recurso com um endereço para uma versão IP ou outra. Se você quiser dois recursos de endereço IP público, um para cada versão de IP, deverá executar o comando duas vezes, especificando diferentes nomes e versões de IP para os recursos de endereço IP público.

Para obter detalhes adicionais sobre os atributos específicos de um endereço IP público durante a criação, consulte a tabela a seguir.

   |Configuração|Necessário?|Detalhes|
   |---|---|---|
   |Versão IP|Sim| Selecione IPv4 ou IPv6 ou ambos. A seleção de ambos resultará em dois endereços IP públicos sendo criados-1 endereço IPv4 e 1 endereço IPv6. Saiba mais sobre o [IPv6 no Azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU **básico** . Você não pode alterar a SKU depois que o endereço IP público é criado. Uma máquina virtual autônoma, máquinas virtuais em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais podem usar SKUs Básicos ou Standard. Não é permitido misturar SKUs entre máquinas virtuais em conjuntos de disponibilidade ou conjuntos de dimensionamento ou VMs autônomas. SKU **Básico**: se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Nenhuma* por padrão. Os IPs públicos básicos não dão suporte a zonas de disponibilidade. SKU **Standard**: um IP público de SKU Standard pode ser associado a uma máquina virtual ou a um front-end de balanceador de carga. Se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Com redundância de zona* por padrão. Para obter informações sobre zonas de disponibilidade, consulte configuração **Zona de disponibilidade**. O SKU padrão será necessário se você associar o endereço a um Load Balancer Standard. Para saber mais sobre os Load Balancers Standard, consulte [SKU Standard do Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando você atribui um endereço IP público de SKU padrão ao adaptador de rede de uma máquina virtual, deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](./network-security-groups-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.|
   |Camada|Sim|Indica se o endereço IP está associado a uma região (**regional**) ou é "anycast" de várias regiões (**globais**). *Observe que um IP de "camada global" é uma funcionalidade de visualização para IPS padrão e, no momento, utilizado somente para o Load Balancer entre regiões*.|
   |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado.|
   |Atribuição de endereço IP|Sim|**Dinâmico:** endereços dinâmicos são atribuídos somente depois que um endereço IP público é associado a um recurso do Azure e o recurso é iniciado pela primeira vez. Endereços dinâmicos poderão mudar se tiverem sido atribuídos a um recurso, como uma máquina virtual, e a máquina virtual for parada (desalocada) e, em seguida, reiniciada. O endereço permanecerá o mesmo se uma máquina virtual for reinicializada ou interrompida (mas não desalocada). Os endereços dinâmicos são liberados quando um recurso de endereço IP público é desassociado de um recurso que está associado. **Estático:** endereços estáticos são atribuídos quando um endereço IP público é criado. Endereços estáticos não são liberados até que um recurso de endereço IP público seja excluído. Se o endereço não estiver associado a um recurso, você poderá alterar o método de atribuição depois que o endereço for criado. Se o endereço estiver associado a um recurso, você não poderá alterar o método de atribuição. Se você selecionar *IPv6* para a **versão de IP**, o método de atribuição deverá ser *dinâmico* para o SKU básico.  Os endereços padrão do SKU são *estáticos* para IPv4 e IPv6. |
   |Tempo limite de ociosidade (minutos)|Não|Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive. Se você selecionar IPv6 para a **versão IP**, esse valor não poderá ser alterado. |
   |Rótulo do nome DNS|Não|Deve ser exclusivo mo local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O Azure registra automaticamente o nome e o endereço IP no DNS dele para que você possa se conectar a um recurso com o nome. O Azure acrescenta *localização.cloudapp.azure.com* (em que localização é a localização que você seleciona) ao nome que você fornece para criar o nome DNS totalmente qualificado. Se você optar por criar duas versões de endereço, o mesmo nome DNS será atribuído aos endereços IPv4 e IPv6. O DNS padrão do Azure contém registros de nome IPv4 e IPv6 AAAA e responde com ambos os registros quando o nome DNS é pesquisado. O cliente escolhe com qual endereço (IPv4 ou IPv6) quer se comunicar. Em vez de ou além de usar o rótulo de nome DNS com o sufixo padrão, você pode usar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que seja resolvido para o endereço IP público. Para obter mais informações, consulte [Usar o DNS do Azure com o endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nome (visível somente se você selecionar a versão de IP de **ambos**)|Sim, se você selecionar a versão IP de **ambos**|O nome deve ser diferente do nome que você insere para o primeiro **Nome** nessa lista. Se você optar por criar endereços IPv4 e IPv6, o portal criará dois recursos de endereço IP públicos separados, cada um com uma versão do endereço IP atribuída a ele.|
   |Atribuição de endereço IP (visível somente se você selecionar a versão de IP de **ambos**)|Sim, se você selecionar a versão IP de **ambos**|Mesmas restrições que a atribuição de endereço IP acima|
   |Subscription|Sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você associará os IPs públicos.|
   |Resource group|Sim|Pode existir no mesmo [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , ou diferente, que o recurso ao qual você associará os IPs públicos.|
   |Localização|Sim|Deve existir no mesmo [local](https://azure.microsoft.com/regions), também conhecido como região, como o recurso ao qual você associará os IPs públicos.|
   |Zona de disponibilidade| Não | Essa configuração só aparecerá se você selecionar uma localização com suporte. Para obter uma lista de localizações com suporte, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você tiver selecionado o SKU **Básico**, *Nenhum* será selecionado automaticamente para você. Se você preferir garantir uma zona específica, você poderá selecionar uma. Qualquer uma dessas opções será sem redundância de zona. Se você tiver selecionado o SKU **Standard**, a opção Com redundância de zona será selecionada automaticamente para você e tornará o seu caminho de dados resiliente a falhas de zona. Se você preferir garantir uma zona específica (que não é resiliente a falhas de zona), você poderá selecionar uma.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Exibir, modificar as configurações ou excluir um endereço IP público

   - **Exibição/lista**: para examinar as configurações de um IP público, incluindo a SKU, o endereço, qualquer associação aplicável (por exemplo, NIC de máquina Virtual, Load Balancer frontend).
   - **Modificar**: para modificar as configurações usando as informações na etapa 4 de [criar um endereço IP público](#create-a-public-ip-address), como o tempo limite de ociosidade, o rótulo de nome DNS ou o método de atribuição.  (Para o processo completo de atualização de um SKU de IP público de básico para Standard, consulte [Atualizar endereços IP públicos do Azure](./virtual-network-public-ip-address-upgrade.md).)
   >[!WARNING]
   >Para alterar a atribuição de um endereço IP público de estático para dinâmico, primeiro você deve dissociar o endereço de todas as configurações de IP aplicáveis (consulte a seção **excluir** ).  Observe também que quando você altera o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (caso tenha definido um), um endereço IP dinâmico pode ser alterado quando a máquina virtual é iniciada depois de ter ficado no estado interrompida (desalocada). Para impedir que o endereço seja alterado, atribua um endereço IP estático.
   
|Operação|Portal do Azure|Azure PowerShell|CLI do Azure|
|---|---|---|---|
|Visualizar | Na seção **visão geral** de um IP público |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um objeto de endereço IP público e exibir suas configurações| [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar as configurações|
|List | Na categoria **endereços IP públicos** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um ou mais objetos de endereço IP público e exibir suas configurações|[AZ Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) para listar endereços IP públicos|
|Modificar | Para um IP dissociado, selecione **configuração** para modificar o tempo limite de ociosidade, rótulo de nome DNS ou alterar a atribuição de IP básico de estático para dinâmico  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para atualizar as configurações |[AZ Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) para atualizar |

   - **Excluir**: a exclusão de IPS públicos requer que o objeto IP público não seja associado a nenhuma configuração de IP ou NIC de máquina virtual. Consulte a tabela abaixo para obter mais detalhes.

|Recurso|Portal do Azure|Azure PowerShell|CLI do Azure|
|---|---|---|---|
|[Máquina Virtual](./remove-public-ip-address-vm.md)|Selecione **dissociar** para dissociar o endereço IP da configuração da NIC e, em seguida, selecione **excluir**.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para dissociar o endereço IP da configuração da NIC; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para excluir|[AZ Network Public-IP Update--remove](/cli/azure/network/public-ip#az-network-public-ip-update) para dissociar o endereço IP da configuração da NIC; [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) para excluir |
|Load Balancer frontend | Navegue até um endereço IP público não usado e selecione **associar** e selecione a Load Balancer com a configuração de IP de front-end relevante para substituí-la (o IP antigo pode ser excluído usando o mesmo método de VM)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) para associar a nova configuração de IP de front-end com Load Balancer públicos; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para excluir; também é possível usar [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) para remover a configuração de IP de front-end se houver mais de um |[AZ Network lb frontend-IP Update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) para associar a nova configuração de IP de front-end com Load Balancer públicos; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para excluir; também é possível usar [AZ Network lb frontend-IP Delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) para remover a configuração de IP de front-end se houver mais de um|
|Firewall|N/D| [Desalocar ()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) para desalocar o firewall e remover todas as configurações de IP | [AZ Network firewall IP-config Delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) para remover o IP (mas deve usar o PowerShell para desalocar primeiro)|

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Ao usar um conjunto de dimensionamento de máquinas virtuais com IPs públicos, não há objetos IP públicos separados associados às instâncias de máquina virtual individuais. No entanto, um objeto de prefixo de IP público [pode ser usado para gerar os IPs da instância](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Para listar os IPs públicos em um conjunto de dimensionamento de máquinas virtuais, você pode usar o PowerShell ([Get-AzPublicIpAddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) ou a CLI ([AZ vmss List-Instance-Public-IPS](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Para saber mais, confira [Rede para os conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público aos recursos a seguir:

- Uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ao criar) ou para uma [máquina virtual existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Load Balancer público](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de Aplicativo](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um gateway de VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicas, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                                             | Nome                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Ler um endereço IP público                                          |
| Microsoft.Network/publicIPAddresses/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresses/delete                         | Excluir um endereço IP público                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associar um recurso de endereço IP público a um recurso                    |

## <a name="next-steps"></a>Próximas etapas

- Criar um endereço IP público usando os scripts de exemplo do [PowerShell](powershell-samples.md) ou da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para endereços IP públicos
