---
title: "Configurar endereços IP para um adaptador de rede do Azure | Microsoft Docs"
description: "Saiba como adicionar, alterar e remover endereços IP públicos e privados para um adaptador de rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 254d5d43f0f665f64ddfe276fe31702f66f16758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Adicionar, alterar ou remover endereços IP para um adaptador de rede do Azure

Saiba como adicionar, alterar e remover endereços IP públicos e privados para um adaptador de rede. Endereços IP privados atribuídos a um adaptador de rede permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual do Azure e redes conectadas. Um endereço IP privado também permite a comunicação de saída com a Internet usando um endereço IP imprevisível. Um [Endereço IP público](virtual-network-public-ip-address.md) atribuído a um adaptador de rede permite a comunicação de entrada com uma máquina virtual a partir da Internet. O endereço também permite a comunicação de saída da máquina virtual com a Internet usando um endereço IP previsível. Para obter detalhes, confira [Entender as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Se você precisar criar, alterar ou excluir um adaptador de rede, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Se você precisar adicionar, ou remover, adaptadores de rede de uma máquina virtual, leia o artigo [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md). 


## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites para endereços IP públicos e privados.
- Faça logon no [Portal](https://portal.azure.com) do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se usar comandos do PowerShell para concluir as tarefas neste artigo, [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você for usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no botão **>_** do Cloud Shell na parte superior do [Portal](https://portal.azure.com).

## <a name="add-ip-addresses"></a>Adicionar endereços IP

Você pode adicionar quantos endereços [IPv4](#ipv4) [privados](#private) e [públicos](#public) forem necessários a um adaptador de rede, desde que respeite os limites listados no artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Não é possível usar o portal para adicionar um endereço IPv6 a um adaptador de rede existente (embora seja possível usar o portal para adicionar um endereço IPv6 privado durante a criação do adaptador de rede). Use o PowerShell ou a CLI para adicionar um endereço IPv6 privado a uma [configuração de IP secundário](#secondary) (desde que não haja uma configuração de IP secundário) de um adaptador de rede não anexado a uma máquina virtual. Não é possível usar qualquer ferramenta para adicionar um endereço IPv6 público a um adaptador de rede. Confira [IPv6](#ipv6) para obter detalhes sobre como usar endereços IPv6. 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** exibida, clique no adaptador de rede ao qual deseja adicionar um endereço IPv4.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha do adaptador de rede selecionado.
5. Clique em **+ Adicionar** na folha de configurações de IP que é exibida.
6. Especifique o seguinte e clique em **OK** para fechar a folha **Adicionar configuração de IP**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |Nome|Sim|Deve ser exclusivo ao adaptador de rede|
    |Tipo|Sim|Como você está adicionando uma configuração de IP a um adaptador de rede existente, e cada adaptador de rede deve ter uma configuração de IP [primária](#primary), sua única opção é **Secundária**.|
    |Método de atribuição de endereço IP privado|Sim|Os endereços [**dinâmicos**](#dynamic) podem mudar se a máquina virtual for reiniciada após permanecer em um estado de interrompida (desalocada). O Azure atribui um endereço disponível do espaço de endereços da sub-rede à qual o adaptador de rede está conectado. Os endereços [**estáticos**](#static) não serão liberados até que o adaptador de rede seja excluído. Especifique um endereço IP do intervalo de espaço de endereço da sub-rede que não esteja em uso por outra configuração de IP.|
    |Endereço IP público|Não|**Desabilitado:** no momento, nenhum recurso de endereço IP público está associado à configuração de IP. **Habilitado:** selecione um endereço IP público IPv4 existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).|
7. Adicione manualmente endereços IP privados secundários ao sistema operacional da máquina virtual seguindo as instruções do artigo [Como atribuir vários endereços IP a sistemas operacionais de máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Confira endereços IP [privados](#private) para ver considerações especiais antes de adicionar manualmente os endereços IP ao sistema operacional de uma máquina virtual. Não adicione endereços IP públicos ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Alterar configurações de endereço IP

Você pode precisar alterar o método de atribuição de endereço IPv4, alterar o endereço IPv4 estático ou alterar o endereço IP público atribuído a um adaptador de rede. Se você estiver alterando o endereço IPv4 privado de uma configuração de IP secundário associada a um adaptador de rede secundário em uma máquina virtual (saiba mais sobre [adaptadores de rede primário e secundário](virtual-network-network-interface-vm.md#about)), coloque a máquina virtual no estado interrompido (desalocado) antes de concluir as etapas a seguir: 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique no adaptador de rede que você deseja exibir ou do qual deseja alterar as configurações de endereço IP.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha do adaptador de rede selecionado.
5. Clique na configuração de IP que você deseja modificar na lista da folha que será aberta para as configurações de IP.
6. Altere as configurações, conforme desejado, usando as informações sobre as configurações na etapa 6 da seção [Adicionar uma configuração de IP](#create-ip-config) deste artigo. Clique em **Salvar** para fechar a folha de configuração de IP alterada.

>[!NOTE]
>Se o adaptador de rede primário tiver várias configurações de IP, e você alterar o endereço IP privado da configuração de IP primário, será necessário reatribuir manualmente todos os endereços IP secundários para o adaptador de rede no Windows (isso não é necessário para Linux). Para atribuir manualmente endereços IP a um adaptador de rede em um sistema operacional, leia o artigo [Como atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Confira endereços IP [privados](#private) para ver considerações especiais antes de adicionar manualmente os endereços IP ao sistema operacional de uma máquina virtual. Não adicione endereços IP públicos ao sistema operacional da máquina virtual.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Remover endereços IP

Você pode remover endereços IP [privados](#private) e [públicos ](#public) de um adaptador de rede, mas um adaptador de rede deve sempre ter pelo menos um endereço IPv4 privado atribuído a ele.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** exibida, clique no adaptador de rede do qual você deseja remover endereços IP.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha do adaptador de rede selecionado.
5. Clique com o botão direito em uma configuração de IP [secundária](#secondary) (não é possível excluir a configuração [primária](#primary)) que você deseja excluir, clique em **Excluir** e, depois, clique em **Sim** para confirmar a exclusão. Se a configuração tem um recurso de endereço IP público associado a ela, o recurso é dissociado da configuração de IP, mas o recurso não é excluído.
6. Feche a folha das **configurações de IP**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>Configurações de IP

Os endereços IP [privados](#private) e (opcionalmente) [públicos](#public) são atribuídos a uma ou mais configurações de IP atribuídas a um adaptador de rede. Há dois tipos de configurações de IP:

### <a name="primary"></a>Primário

Cada adaptador de rede recebe uma configuração de IP primário. Uma configuração de IP primário:

- Tem um endereço [privado](#private) [IPv4](#ipv4) atribuído a ela. Não é possível atribuir um endereço [IPv6](#ipv6) privado a uma configuração de IP primário.
- Também pode ter tem um endereço IPv4 [público](#public) atribuído a ela. Não é possível atribuir um endereço IPv6 público a uma configuração de IP secundário. No entanto, você pode atribuir um endereço IPv6 público a um balanceador de carga do Azure, que pode equilibrar o tráfego para o endereço IPv6 privado de uma máquina virtual. Para saber mais, confira [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Secundário

Além de uma configuração de IP primário, um adaptador de rede pode ter várias ou nenhuma configuração de IP secundário atribuída a ele. Uma configuração de IP secundário:

- Deve ter um endereço IPv4 ou IPv6 privado atribuído a ela. Se o endereço for IPv6, o adaptador de rede poderá ter apenas uma configuração de IP secundário. Se o endereço for IPv4, o adaptador de rede poderá ter várias configurações de IP secundário atribuídas a ele. Para saber mais sobre quantos endereços IPv4 públicos e privados podem ser atribuídos a um adaptador de rede, confira o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  
- Também pode ter um endereço IPv4 público atribuído a ela, se o endereço IP privado for IPv4. Se o endereço IP privado for IPv6, não será possível atribuir um endereço IPv4 ou IPv6 público para a configuração de IP. Atribuir vários endereços IP a um adaptador de rede é útil em cenários como:
    - Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
    - Uma máquina virtual usada como dispositivo de rede virtual, como um firewall ou balanceador de carga.
    - A capacidade de adicionar qualquer um dos endereços IPv4 privados para qualquer um dos adaptadores de rede a um pool de back-ends do Azure Load Balancer. No passado, somente o endereço IPv4 primário para o adaptador de rede primário podia ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IPv4, confira o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    - A capacidade de balancear a carga de um endereço IPv6 atribuído a um adaptador de rede. Para saber mais sobre como balancear a carga de um endereço IPv6, confira o artigo [Balancear a carga de endereços IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


## <a name="address-types"></a>Tipos de endereço

Você pode atribuir os seguintes tipos de endereços IP a uma [configuração de IP](#ip-configurations):

### <a name="private"></a>Privado

Os endereços [IPv4](#ipv4) privados permitem que uma máquina virtual se comunique com outros recursos em uma rede virtual ou outras redes conectadas. Uma máquina virtual não pode se comunicar internamente, nem externamente com um endereço [IPv6](#ipv6) privado, com uma exceção. Uma máquina virtual pode se comunicar com o balanceador de carga do Azure usando um endereço IPv6. Para saber mais, confira [detalhes e limitações do IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Por padrão, os servidores DHCP do Azure atribuem o endereço IPv4 privado à [configuração de IP primário](#primary) do adaptador de rede ao adaptador de rede dentro do sistema operacional da máquina virtual. A menos que seja necessário, nunca defina manualmente o endereço IP de um adaptador de rede no sistema operacional da máquina virtual. 

> [!WARNING]
> Se o endereço IPv4 definido como o endereço IP primário de um adaptador de rede no sistema operacional de uma máquina virtual for sempre diferente de endereço IPv4 privado atribuído à configuração de IP primário do adaptador de rede primário anexado a uma máquina virtual no Azure, você perderá a conectividade com a máquina virtual.

Há cenários nos quais é necessário definir manualmente o endereço IP de um adaptador de rede no sistema operacional da máquina virtual. Por exemplo, você deve definir manualmente os endereços IP primários e secundários de um sistema operacional Windows ao adicionar vários endereços IP a uma máquina virtual do Azure. Para uma máquina virtual Linux, talvez você só precise definir manualmente os endereços IP secundários. Confira [Adicionar endereços IP a um sistema operacional de VM](virtual-network-multiple-ip-addresses-portal.md#os-config) para obter detalhes. Quando você define manualmente o endereço IP dentro do sistema operacional, é recomendável sempre atribuir os endereços à configuração de IP de um adaptador de rede usando o método estático (em vez de dinâmico) de atribuição. A atribuição do endereço usando o método estático assegura que o endereço não mude no Azure. Se algum dia você precisar alterar o endereço atribuído a uma configuração de IP, é recomendável que você:

1. A fim de garantir que a máquina virtual receba um endereço de servidores DHCP do Azure, altere a atribuição do endereço IP novamente para DHCP dentro do sistema operacional e reinicie a máquina virtual.
2. Interrompa (desaloque) a máquina virtual.
3. Altere o endereço IP da configuração de IP no Azure.
4. Iniciar a máquina virtual.
5. [Configure manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) os endereços IP secundários dentro do sistema operacional (e também o endereço IP primário no Windows) para corresponder com o que você definiu no Azure.
 
Com as etapas anteriores, o endereço IP atribuído ao adaptador de rede no Azure, e no sistema de operacional de uma máquina virtual. Para controlar em quais máquinas virtuais dentro de sua assinatura você definiu manualmente os endereços IP dentro de um sistema operacional, considere a adição de uma [marca](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) do Azure às máquinas virtuais. Você pode usar "Atribuição de endereço IP: estático", por exemplo. Dessa forma, você pode localizar facilmente, dentro de sua assinatura, as máquinas virtuais para as quais você definiu manualmente o endereço IP para dentro do sistema operacional.

Além de permitir que uma máquina virtual se comunique com outros recursos na mesma rede, ou em redes virtuais conectadas, um endereço IP privado também permite que uma máquina virtual se comunique externamente com a Internet. Conexões de saída são endereços de rede de origem convertidos pelo Azure em um endereço IP público imprevisível. Para saber mais sobre a conectividade de Internet de saída do Azure, leia o artigo [Conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você não pode realizar comunicação de entrada com o endereço IP privado de uma máquina virtual pela Internet.

### <a name="public"></a>Público

Os endereços IP públicos habilitam a conectividade de entrada para uma máquina virtual da Internet. As conexões de saída com a Internet usam um endereço IP previsível. Para obter detalhes, confira [Entender as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode atribuir um endereço IP público a uma configuração de IP, mas isso não é necessário. Se você não atribuir um endereço IP público a uma máquina virtual, ela ainda poderá se comunicar externamente com a Internet usando seu endereço IP privado. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md).

Há limites para o número de endereços IP públicos e privados que você pode atribuir a um adaptador de rede. Leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.

> [!NOTE]
> O Azure converte o endereço IP privado de uma máquina virtual em um endereço IP público. Como resultado, o sistema operacional não fica ciente dos endereços IP públicos atribuídos a ele, portanto, nunca será necessário atribuir manualmente um endereço IP público dentro do sistema operacional.

## <a name="assignment-methods"></a>Métodos de atribuição

Os endereços IP públicos e privados são atribuídos usando os seguintes métodos de atribuição:

### <a name="dynamic"></a>Dinâmico

Endereços IPv4 e (opcionalmente) IPv6 privados dinâmicos são atribuídos por padrão. Os endereços dinâmicos poderão mudar se a máquina virtual for colocada em estado interrompido (desalocado) e então reiniciada. Se você não quiser que os endereços IPv4 mudem permanentemente para a máquina virtual, atribua os endereços usando o método estático. Você só pode atribuir um endereço IPv6 privado usando o método de atribuição dinâmica. Não é possível atribuir um endereço IPv6 público a uma configuração de IP usando qualquer método.

### <a name="static"></a>estático

Os endereços atribuídos usando o método estático não mudam até que uma máquina virtual seja excluída. Atribua manualmente um endereço IPv4 privado estático a uma configuração de IP a partir do espaço de endereços da sub-rede na qual reside o adaptador de rede. Você pode, como opção, atribuir um endereço IPv4 estático público ou privado a uma configuração de IP. Não é possível atribuir um endereço IPv6 estático público ou privado a uma configuração de IP. Para saber mais sobre como o Azure atribui endereços IPv4 estáticos públicos, confira o artigo [Endereço IP público](virtual-network-public-ip-address.md).

## <a name="ip-address-versions"></a>Versões de endereço IP

Especifique as seguintes versões ao atribuir endereços:

### <a name="ipv4"></a>IPv4

Cada adaptador de rede deve ter uma configuração de IP [primário](#primary) com um endereço [IPv4](#ipv4) [privado](#private) atribuído. Você pode adicionar uma ou mais configurações de IP [secundário](#secondary) que possuem um endereço IPv4 privado e (opcionalmente) um IPv4 [público](#public).

### <a name="ipv6"></a>IPv6

Você pode atribuir um ou nenhum endereço [IPv6](#ipv6) privado a uma configuração de IP secundário de um adaptador de rede. O adaptador de rede não pode ter configurações de IP secundário. Não é possível adicionar uma configuração de IP com um endereço IPv6 usando o portal. Use o PowerShell ou a CLI para adicionar uma configuração de IP com um endereço IPv6 privado a um adaptador de rede existente. O adaptador de rede não pode ser conectado a uma VM existente.

> [!NOTE]
> Embora você possa criar uma interface de rede com um endereço IPv6 usando o portal, você não pode adicionar uma interface de rede existente para uma máquina virtual nova ou existente, usando o portal. Use o PowerShell ou a CLI 2.0 do Azure para criar um adaptador de rede com um endereço IPv6 privado, e depois anexá-lo ao criar uma máquina virtual. Não é possível anexar um adaptador de rede, com um endereço IPv6 privado atribuído a ele, a uma máquina virtual existente. Você não pode adicionar um endereço IPv6 privado a uma configuração de IP de qualquer adaptador de rede conectado a uma máquina virtual usando quaisquer ferramentas (portal, CLI ou PowerShell).

Não é possível atribuir um endereço IPv6 público a uma configuração de IP secundário.

## <a name="skus"></a>SKUs

Um endereço IP público é criado com o SKU Básico ou Standard.  Para obter mais detalhes sobre as diferenças em SKUs, consulte [Gerenciar endereços IP públicos](virtual-network-public-ip-address.md).

> [!NOTE]
> Quando você atribui um endereço IP público de SKU Standard ao adaptador de rede de uma máquina virtual, você deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual com diferentes configurações de IP, leia os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
