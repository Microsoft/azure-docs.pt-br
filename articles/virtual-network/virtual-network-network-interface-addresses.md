---
title: Configurar endereços IP para um adaptador de rede do Azure | Microsoft Docs
description: Saiba como adicionar, alterar e remover endereços IP públicos e privados para um adaptador de rede.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 1df132e558421d2ec6e26c3883c89457716dfc42
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419007"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Adicionar, alterar ou remover endereços IP para um adaptador de rede do Azure

Saiba como adicionar, alterar e remover endereços IP públicos e privados para um adaptador de rede. Endereços IP privados atribuídos a um adaptador de rede permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual do Azure e redes conectadas. Um endereço IP privado também permite a comunicação de saída com a Internet usando um endereço IP imprevisível. Um [Endereço IP público](virtual-network-public-ip-address.md) atribuído a um adaptador de rede permite a comunicação de entrada com uma máquina virtual a partir da Internet. O endereço também permite a comunicação de saída da máquina virtual com a Internet usando um endereço IP previsível. Para obter detalhes, confira [Entender as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Se você precisar criar, alterar ou excluir um adaptador de rede, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Se você precisar adicionar, ou remover, adaptadores de rede de uma máquina virtual, leia o artigo [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta que você realizou o logon, ou conectou ao Azure, deve estar atribuída à função do [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída a ações adequadas listadas em [Permissões de interface de rede](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Você pode adicionar quantos endereços [IPv4](#ipv4) [públicos](#public) e [privados](#private) forem necessários para uma interface de rede, dentro dos limites listados no artigo [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Você pode adicionar um endereço IPv6 privado a uma [configuração de IP secundário](#secondary) (desde que não haja nenhuma configuração de IP secundário existente) para uma interface de rede existente. Cada interface de rede pode ter no máximo um endereço IPv6 privado. Opcionalmente, você pode adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6. Confira [IPv6](#ipv6) para obter detalhes sobre como usar endereços IPv6.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione a interface de rede que você deseja exibir ou alterar as configurações da lista.
3. Em **CONFIGURAÇÕES**, selecione **Configurações de IP**.
4. Em **Configurações de IP**, selecione **+ Adicionar**.
5. Especifique o seguinte, em seguida, selecione **OK**:

   |Configuração|Necessário?|Detalhes|
   |---|---|---|
   |Nome|Sim|Deve ser exclusivo ao adaptador de rede|
   |Tipo|Sim|Como você está adicionando uma configuração de IP a um adaptador de rede existente, e cada adaptador de rede deve ter uma configuração de IP [primária](#primary), sua única opção é **Secundária**.|
   |Método de atribuição de endereço IP privado|Sim|[**Dinâmico**](#dynamic): o Azure atribui o próximo endereço disponível para o intervalo de endereços de sub-rede na qual o adaptador de rede está implantado. [**Estático**](#static): você atribui um endereço não utilizado ao intervalo de endereços de sub-rede na qual o adaptador de rede está implantado.|
   |Endereço IP público|Não|**Desabilitado:** no momento, nenhum recurso de endereço IP público está associado à configuração de IP. **Habilitado:** selecione um endereço IP público IPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).|
6. Adicione manualmente endereços IP privados secundários ao sistema operacional da máquina virtual seguindo as instruções do artigo [Como atribuir vários endereços IP a sistemas operacionais de máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Confira endereços IP [privados](#private) para ver considerações especiais antes de adicionar manualmente os endereços IP ao sistema operacional de uma máquina virtual. Não adicione endereços IP públicos ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Alterar configurações de endereço IP

Você pode precisar alterar o método de atribuição de endereço IPv4, alterar o endereço IPv4 estático ou alterar o endereço IP público atribuído a um adaptador de rede. Se você estiver alterando o endereço IPv4 privado de uma configuração de IP secundário associada a um adaptador de rede secundário em uma máquina virtual (saiba mais sobre [adaptadores de rede primário e secundário](virtual-network-network-interface-vm.md)), coloque a máquina virtual no estado interrompido (desalocado) antes de concluir as etapas a seguir:

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione a interface de rede que você quer para visualizar ou mudar as configurações de endereço IP da lista.
3. Em **CONFIGURAÇÕES**, selecione **Configurações de IP**.
4. Selecione a configuração de IP que você deseja modificar na lista.
5. Altere as configurações, conforme desejado, usando as informações sobre as configurações na etapa 5 de [Adicionar uma configuração de IP](#add-ip-addresses).
6. Clique em **Salvar**.

>[!NOTE]
>Se o adaptador de rede primário tiver várias configurações de IP, e você alterar o endereço IP privado da configuração de IP primário, será necessário reatribuir manualmente todos os endereços IP secundários para o adaptador de rede no Windows (isso não é necessário para Linux). Para atribuir manualmente endereços IP a uma interface de rede em um sistema operacional, consulte [Atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Confira endereços IP [privados](#private) para ver considerações especiais antes de adicionar manualmente os endereços IP ao sistema operacional de uma máquina virtual. Não adicione endereços IP públicos ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Você pode remover endereços IP [privados](#private) e [públicos ](#public) de um adaptador de rede, mas um adaptador de rede deve sempre ter pelo menos um endereço IPv4 privado atribuído a ele.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione a interface de rede que você deseja remover os endereços IP da lista.
3. Em **CONFIGURAÇÕES**, selecione **Configurações de IP**.
4. Selecione com o botão direito em uma configuração de IP [secundária](#secondary) (não é possível excluir a configuração [primária](#primary)) que você deseja excluir, selecione **Excluir** e, depois, selecione **Sim** para confirmar a exclusão. Se a configuração tem um recurso de endereço IP público associado a ela, o recurso é dissociado da configuração de IP, mas o recurso não é excluído.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configurações de IP

Os endereços IP [privados](#private) e (opcionalmente) [públicos](#public) são atribuídos a uma ou mais configurações de IP atribuídas a um adaptador de rede. Há dois tipos de configurações de IP:

### <a name="primary"></a>Primário

Cada adaptador de rede recebe uma configuração de IP primário. Uma configuração de IP primário:

- Tem um endereço [privado](#private) [IPv4](#ipv4) atribuído a ela. Não é possível atribuir um endereço [IPv6](#ipv6) privado a uma configuração de IP primário.
- Também pode ter tem um endereço IPv4 [público](#public) atribuído a ela. Você não pode atribuir um endereço IPv6 público a uma configuração de IP primário (IPv4). 

### <a name="secondary"></a>Secundário

Além de uma configuração de IP primário, um adaptador de rede pode ter várias ou nenhuma configuração de IP secundário atribuída a ele. Uma configuração de IP secundário:

- Deve ter um endereço IPv4 ou IPv6 privado atribuído a ela. Se o endereço for IPv6, o adaptador de rede poderá ter apenas uma configuração de IP secundário. Se o endereço for IPv4, o adaptador de rede poderá ter várias configurações de IP secundário atribuídas a ele. Para saber mais sobre quantos endereços IPv4 públicos e privados podem ser atribuídos a um adaptador de rede, confira o artigo [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Também pode ter um endereço IPv4 ou IPv6 público atribuído a ele. A atribuição de vários endereços IPv4 a uma interface de rede é útil em cenários como:
  - Hospedagem de vários sites ou serviços com diferentes endereços IP e certificados TLS/SSL em um único servidor.
  - Uma máquina virtual usada como dispositivo de rede virtual, como um firewall ou balanceador de carga.
  - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer um dos adaptadores de rede a um pool de back-ends do Azure Load Balancer. No passado, somente o endereço IPv4 primário para o adaptador de rede primário podia ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IPv4, confira o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
  - A capacidade de balancear a carga de um endereço IPv6 atribuído a um adaptador de rede. Para saber mais sobre como balancear a carga de um endereço IPv6, confira o artigo [Balancear a carga de endereços IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="address-types"></a>Tipos de endereço

Você pode atribuir os seguintes tipos de endereços IP a uma [configuração de IP](#ip-configurations):

### <a name="private"></a>Privados

Os endereços [IPv4](#ipv4) ou IPv6 privados permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual ou em outras redes conectadas. 

Por padrão, os servidores DHCP do Azure atribuem o endereço IPv4 privado à [configuração de IP primário](#primary) do adaptador de rede do Azure ao adaptador de rede dentro do sistema operacional da máquina virtual. A menos que seja necessário, nunca defina manualmente o endereço IP de um adaptador de rede no sistema operacional da máquina virtual.

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de um adaptador de rede no sistema operacional de uma máquina virtual for sempre diferente de endereço IPv4 privado atribuído à configuração de IP primário do adaptador de rede primário anexado a uma máquina virtual no Azure, você perderá a conectividade com a máquina virtual.

Há cenários nos quais é necessário definir manualmente o endereço IP de um adaptador de rede no sistema operacional da máquina virtual. Por exemplo, você deve definir manualmente os endereços IP primários e secundários de um sistema operacional Windows ao adicionar vários endereços IP a uma máquina virtual do Azure. Para uma máquina virtual Linux, talvez você só precise definir manualmente os endereços IP secundários. Confira [Adicionar endereços IP a um sistema operacional de VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter detalhes. Se algum dia você precisar alterar o endereço atribuído a uma configuração de IP, é recomendável que você:

1. Certifique-se de que a máquina virtual está recebendo um endereço de servidores DHCP do Azure. Depois, altere a atribuição do endereço IP novamente para DHCP dentro do sistema operacional e reinicie a máquina virtual.
2. Interrompa (desaloque) a máquina virtual.
3. Altere o endereço IP da configuração de IP no Azure.
4. Iniciar a máquina virtual.
5. [Configure manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operacional (e também o endereço IP primário no Windows) para corresponder com o que você definiu no Azure.

Com as etapas anteriores, o endereço IP atribuído ao adaptador de rede no Azure, e no sistema de operacional de uma máquina virtual. Para controlar em quais máquinas virtuais dentro de sua assinatura você definiu manualmente os endereços IP dentro de um sistema operacional, considere a adição de uma [marca](../azure-resource-manager/management/tag-resources.md) do Azure às máquinas virtuais. Você pode usar "Atribuição de endereço IP: estático", por exemplo. Dessa forma, você pode localizar facilmente, dentro de sua assinatura, as máquinas virtuais para as quais você definiu manualmente o endereço IP para dentro do sistema operacional.

Além de permitir que uma máquina virtual se comunique com outros recursos na mesma rede, ou em redes virtuais conectadas, um endereço IP privado também permite que uma máquina virtual se comunique externamente com a Internet. Conexões de saída são endereços de rede de origem convertidos pelo Azure em um endereço IP público imprevisível. Para saber mais sobre a conectividade de Internet de saída do Azure, leia o artigo [Conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você não pode realizar comunicação de entrada com o endereço IP privado de uma máquina virtual pela Internet. Se as conexões de saída exigirem um endereço IP público previsível, associe um recurso de endereço IP público a um adaptador de rede.

### <a name="public"></a>Público

Os endereços IP públicos atribuídos por meio de um recurso de endereço IP público habilitam a conectividade de entrada com uma máquina virtual pela Internet. As conexões de saída com a Internet usam um endereço IP previsível. Para obter detalhes, confira [Entender as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode atribuir um endereço IP público a uma configuração de IP, mas isso não é necessário. Se você não atribuir um endereço IP público a uma máquina virtual associando um recurso de endereço IP público, a máquina virtual ainda poderá se comunicar na saída com a Internet. Nesse caso, o endereço IP privado é o endereço de rede de origem convertido pelo Azure em um endereço IP público imprevisível. Para saber mais sobre os recursos de endereço IP público, consulte [Recurso de endereço IP público](virtual-network-public-ip-address.md).

Há limites para o número de endereços IP públicos e privados que você pode atribuir a um adaptador de rede. Leia o artigo [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.

> [!NOTE]
> O Azure converte o endereço IP privado de uma máquina virtual em um endereço IP público. Como resultado, o sistema operacional de uma máquina virtual não fica ciente dos endereços IP públicos atribuídos a ele e, portanto, não há necessidade de atribuir manualmente um endereço IP público dentro do sistema operacional.

## <a name="assignment-methods"></a>Métodos de atribuição

Os endereços IP públicos e privados são atribuídos usando um dos seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmico

Endereços IPv4 e (opcionalmente) IPv6 privados dinâmicos são atribuídos por padrão.

- **Somente público**: o Azure atribui o endereço de um intervalo exclusivo a cada região do Azure. Para saber quais intervalos são atribuídos a cada região, consulte [Intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). O endereço pode ser alterado quando uma máquina virtual é parada (desalocada) e, em seguida, iniciada novamente. Não é possível atribuir um endereço IPv6 público a uma configuração de IP usando um desses métodos de atribuição.
- **Somente privado**: o Azure reserva os primeiros quatro endereços em cada intervalo de endereços de sub-rede e não atribui os endereços. O Azure atribui o próximo endereço disponível a um recurso no intervalo de endereços de sub-rede. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16 e os endereços 10.0.0.4-10.0.0.14 já estiverem atribuídos (. 0-.3 são reservados), o Azure atribuirá 10.0.0.15 ao recurso. Dinâmico é o método de alocação padrão. Uma vez atribuído, os endereços IP dinâmicos só são liberados se um adaptador de rede é excluído, atribuído a uma sub-rede diferente na mesma rede virtual ou se o método de alocação é alterado para estático e um endereço IP diferente é especificado. Por padrão, o Azure atribui o endereço anterior atribuído dinamicamente como o endereço estático quando você altera o método de alocação de dinâmico para estático. 

### <a name="static"></a>Estático

Você pode (opcionalmente) atribuir um endereço IPv4 ou IPv6 estático público ou privado a uma configuração de IP. Para saber mais sobre como o Azure atribui endereços IPv4 estáticos públicos, confira [Endereço IP público](virtual-network-public-ip-address.md).

- **Somente público**: o Azure atribui o endereço de um intervalo exclusivo a cada região do Azure. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure. O endereço não é alterado até que o recurso de endereço IP público ao qual ele foi atribuído seja excluído ou o método de atribuição seja alterado para dinâmico. Se o recurso de endereço IP público estiver associado a uma configuração de IP, ele deverá ser desassociado da configuração de IP antes da alteração de seu método de atribuição.
- **Somente privado**: você seleciona e atribui um endereço do intervalo de endereços da sub-rede. O endereço que você atribuir pode ser qualquer endereço no intervalo de endereços da sub-rede que não seja um dos quatro primeiros endereços no intervalo de endereços da sub-rede e não esteja atualmente atribuído a nenhum outro recurso na sub-rede. Os endereços estáticos só são liberados se um adaptador de rede é excluído. Se você alterar o método de alocação para estático, o Azure atribuirá dinamicamente o endereço IP dinâmico atribuído anteriormente como o endereço estático, mesmo que o endereço não seja o próximo endereço disponível no intervalo de endereços da sub-rede. O endereço também será alterado se o adaptador de rede for atribuído a uma sub-rede diferente na mesma rede virtual. No entanto, para atribuir o adaptador de rede a outra sub-rede, primeiro você deve alterar o método de alocação de estático para dinâmico. Depois de atribuir o adaptador de rede a uma sub-rede diferente, você poderá alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

## <a name="ip-address-versions"></a>Versões de endereço IP

Especifique as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada interface de rede deve ter uma configuração de IP [primário](#primary) com um endereço [IPv4](#ipv4) [privado](#private) atribuído. Você pode adicionar uma ou mais configurações de IP [secundário](#secondary) que possuem um endereço IPv4 privado e (opcionalmente) um IPv4 [público](#public).

### <a name="ipv6"></a>IPv6

Você pode atribuir um ou nenhum endereço [IPv6](#ipv6) privado a uma configuração de IP secundário de um adaptador de rede. O adaptador de rede não pode ter configurações de IP secundário. Cada interface de rede pode ter no máximo um endereço IPv6 privado. Opcionalmente, você pode adicionar um endereço IPv6 público a uma configuração de interface de rede IPv6.

> [!NOTE]
> Embora você possa criar uma interface de rede com um endereço IPv6 usando o portal, você não pode adicionar uma interface de rede existente para uma máquina virtual nova ou existente, usando o portal. Use o PowerShell ou a CLI do Azure para criar uma interface de rede com um endereço IPv6 privado e, em seguida, conecte a interface de rede ao criar uma máquina virtual. Não é possível anexar um adaptador de rede, com um endereço IPv6 privado atribuído a ele, a uma máquina virtual existente. Você não pode adicionar um endereço IPv6 privado a uma configuração de IP de qualquer adaptador de rede conectado a uma máquina virtual usando quaisquer ferramentas (portal, CLI ou PowerShell).

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU Básico ou Standard. Para obter mais informações sobre as diferenças em SKUs, consulte [Gerenciar endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando você atribui um endereço IP público de SKU padrão ao adaptador de rede de uma máquina virtual, deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](./network-security-groups-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual com diferentes configurações de IP, leia os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com várias interfaces de rede|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|