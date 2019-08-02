---
title: Criar, alterar ou excluir um endereço IP público do Azure | Microsoft Docs
description: Aprenda a criar, alterar ou excluir um endereço IP público.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 98810af66b0be6d925229b7e05dc01f62106e7cd
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64728738"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou excluir um endereço IP público

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

A conta em que você realizou o logon, ou se conectou ao Azure, deve estar atribuída à função do [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída a ações adequadas listadas em [Permissões](#permissions).

Os endereços IP públicos têm um encargo nominal. Para ver os preços, acesse a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

1. No canto superior esquerdo do Portal, selecione **+ Criar um recurso**.
2. Digite *endereço ip público* em *Pesquisar na caixa Marketplace*. Quando os **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
3. Em **endereço IP público**, selecione **Criar**.
4. Insira, ou selecione os valores para as configurações a seguir em **Criar endereço IP público**, em seguida, selecione **Criar**:

   |Configuração|Obrigatório?|Detalhes|
   |---|---|---|
   |NOME|Sim|O nome deve ser exclusivo no grupo de recursos selecionado.|
   |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU **Básico**. Você não pode alterar a SKU depois que o endereço IP público é criado. Uma máquina virtual autônoma, máquinas virtuais em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais podem usar SKUs Básicos ou Standard. Não é permitido misturar SKUs entre máquinas virtuais em conjuntos de disponibilidade e aquelas em conjuntos de dimensionamento. SKU **Basic**: se você está criando um endereço IP público em uma região que oferece suporte a zonas de disponibilidade, a configuração **Zona de disponibilidade** é definida como *Nenhuma* por padrão. Você pode optar por selecionar uma zona de disponibilidade para assegurar uma zona específica para seu endereço IP público. SKU **Standard**: um IP público de SKU Standard pode ser associado a uma máquina virtual ou a um front-end de balanceador de carga. Se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Com redundância de zona* por padrão. Para obter informações sobre zonas de disponibilidade, consulte configuração **Zona de disponibilidade**. O SKU padrão será necessário se você associar o endereço a um Load Balancer Standard. Para saber mais sobre os Load Balancers Standard, consulte [SKU Standard do Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando você atribui um endereço IP público de SKU padrão ao adaptador de rede de uma máquina virtual, deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.|
   |Versão IP|Sim| Selecione IPv4 ou IPv6. Embora endereços IPv4 públicos possam ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga voltado para a Internet. O balanceador de carga pode balancear o tráfego IPv6 para máquinas virtuais do Azure. Saiba mais sobre [balanceamento de carga de tráfego IPv6 para máquinas virtuais](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você tiver selecionado o **SKU Standard**, você não terá a opção de selecionar *IPv6*. Você só pode criar um endereço IPv4 ao usar o **SKU Standard**.|
   |Atribuição de endereço IP|Sim|**Dinâmico:** endereços dinâmicos são atribuídos somente depois que um endereço IP público é associado a um recurso do Azure e o recurso é iniciado pela primeira vez. Endereços dinâmicos poderão mudar se tiverem sido atribuídos a um recurso, como uma máquina virtual, e a máquina virtual for parada (desalocada) e, em seguida, reiniciada. O endereço permanecerá o mesmo se uma máquina virtual for reinicializada ou interrompida (mas não desalocada). Os endereços dinâmicos são liberados quando um recurso de endereço IP público é desassociado de um recurso que está associado. **Estático:** endereços estáticos são atribuídos quando um endereço IP público é criado. Endereços estáticos não são liberados até que um recurso de endereço IP público seja excluído. Se o endereço não estiver associado a um recurso, você poderá alterar o método de atribuição depois que o endereço for criado. Se o endereço estiver associado a um recurso, você não poderá alterar o método de atribuição. Se você selecionar *IPv6* para a **Versão do IP**, o método de atribuição será *Dinâmico*. Se você selecionar *Standard* para **SKU**, o método de atribuição será *Estático*.|
   |Tempo limite de ociosidade (minutos)|Não|Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive. Se você selecionar IPv6 para a **versão IP**, esse valor não poderá ser alterado. |
   |Rótulo do nome DNS|Não|Deve ser exclusivo mo local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O Azure registra automaticamente o nome e o endereço IP no DNS dele para que você possa se conectar a um recurso com o nome. O Azure acrescenta *localização.cloudapp.azure.com* (em que localização é a localização que você seleciona) ao nome que você fornece para criar o nome DNS totalmente qualificado. Se você optar por criar duas versões de endereço, o mesmo nome DNS será atribuído aos endereços IPv4 e IPv6. O DNS padrão do Azure contém registros de nome IPv4 e IPv6 AAAA e responde com ambos os registros quando o nome DNS é pesquisado. O cliente escolhe com qual endereço (IPv4 ou IPv6) quer se comunicar. Em vez de ou além de usar o rótulo de nome DNS com o sufixo padrão, você pode usar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que seja resolvido para o endereço IP público. Para obter mais informações, consulte [Usar o DNS do Azure com o endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Criar um endereço IPv6 (ou IPv4)|Não| O que é exibido, IPv4 ou IPv6, depende do que você seleciona para a **versão IP**. Por exemplo, se você selecionar **IPv4** para a **versão IP**, **IPv6** será exibido aqui. Se você selecionar *Standard* para **SKU**, você não terá a opção de criar um endereço IPv6.
   |Nome (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)** )|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|O nome deve ser diferente do nome que você insere para o primeiro **Nome** nessa lista. Se você optar por criar endereços IPv4 e IPv6, o portal criará dois recursos de endereço IP públicos separados, cada um com uma versão do endereço IP atribuída a ele.|
   |Atribuição de endereço IP (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)** )|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|Se a caixa de seleção diz **Criar um endereço IPv4**, você pode selecionar um método de atribuição. Se a caixa de seleção diz **Criar um endereço IPv6**, não é possível selecionar um método de atribuição, pois ele deve ser **Dinâmico**.|
   |Assinatura|Sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você deseja associar o endereço IP público.|
   |Grupo de recursos|Sim|Pode existir no mesmo ou em outro [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que o recurso ao qual você deseja associar o endereço IP público.|
   |Local padrão|Sim|Deve existir na mesma [localização](https://azure.microsoft.com/regions), também referenciada como região, que o recurso ao qual você deseja associar o endereço IP público.|
   |Zona de disponibilidade| Não | Essa configuração só aparecerá se você selecionar uma localização com suporte. Para obter uma lista de localizações com suporte, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você tiver selecionado o SKU **Básico**, *Nenhum* será selecionado automaticamente para você. Se você preferir garantir uma zona específica, você poderá selecionar uma. Qualquer uma dessas opções será sem redundância de zona. Se você selecionou o SKU **Standard**: a opção Com redundância de zona é selecionada automaticamente e torna o seu caminho de dados resistente a falhas de zona. Se você preferir garantir uma zona específica (que não é resiliente a falhas de zona), você poderá selecionar uma.

**Comandos**

Embora o portal forneça a opção de criar dois recursos de endereço IP públicos (um IPv4 e um IPv6), os comandos da CLI e do PowerShell a seguir criam um recurso com um endereço para uma versão IP ou o outro. Se você quiser dois recursos de endereço IP público, uma para cada versão IP, deverá executar o comando duas vezes, especificando nomes e versões diferentes para os recursos de endereço IP público.

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Exibir, alterar as configurações ou excluir um endereço IP público

1. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, selecione-o.
2. Selecione o nome do endereço IP público que você quiser visualizar, alterar configurações ou excluir da lista.
3. Preencha uma das opções a seguir, dependendo da ação que deseja: exibir, excluir ou alterar o endereço IP público.
   - **Exibir**: a seção **Visão Geral** mostra as principais configurações do endereço IP público, como o adaptador de rede ao qual ele está associado (caso o endereço esteja associado a um adaptador de rede). O portal não mostra a versão do endereço (IPv4 ou IPv6). Para exibir as informações de versão, use o comando do PowerShell ou da CLI para exibir o endereço IP público. Se a versão do endereço IP for IPv6, o endereço atribuído não será exibido pelo portal, pelo PowerShell nem pela CLI.
   - **Excluir**: para excluir o endereço IP público, selecione **Excluir** na seção **Visão Geral**. Se o endereço estiver associado a uma configuração de IP, ele não poderá ser excluído. Se, no momento, o endereço estiver associado a uma configuração, selecione **Desassociar** para desassociar o endereço da configuração de IP.
   - **Alterar**: selecione **Configuração**. Altere as configurações usando as informações na etapa 4 da seção [Criar um endereço IP público](#create-a-public-ip-address). Para alterar a atribuição para um endereço IPv4 de estático para dinâmico, primeiramente você deve desassociar o endereço IPv4 público da configuração de IP à qual ele está associado. Você pode alterar o método de atribuição para dinâmico e selecione **Associar** para associar o endereço IP à mesma configuração de IP, a uma configuração diferente ou deixá-lo desassociado. Para desassociar um endereço IP público, na seção **Visão Geral**, selecione **Desassociar**.

   >[!WARNING]
   >Ao alterar o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (caso tenha definido um), um endereço IP dinâmico pode ser alterado quando a máquina virtual é iniciada depois de ter ficado no estado interrompida (desalocada). Para impedir que o endereço seja alterado, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) para listar endereços IP públicos; [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar as configurações; [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) para atualizar e [az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) para excluir|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um objeto de endereço IP público e exibir suas configurações [AzPublicIpAddress conjunto](/powershell/module/az.network/set-azpublicipaddress) ao atualizar configurações; [AzPublicIpAddress remover](/powershell/module/az.network/remove-azpublicipaddress) para excluir|

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público aos recursos a seguir:

- Uma VM do [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ao criar uma) ou uma [VM existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de Dimensionamento de Máquinas Virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicas, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                                             | NOME                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Ler um endereço IP público                                          |
| Microsoft.Network/publicIPAddresses/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresses/delete                         | Excluir um endereço IP público                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associar um recurso de endereço IP público a um recurso                    |

## <a name="next-steps"></a>Próximas etapas

- Criar um endereço IP público usando os scripts de exemplo do [PowerShell](powershell-samples.md) ou da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e aplicar a [Política do Azure](policy-samples.md) para endereços IP públicos