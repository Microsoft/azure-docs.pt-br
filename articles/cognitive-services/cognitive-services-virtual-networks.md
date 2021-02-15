---
title: Redes Virtuais
titleSuffix: Azure Cognitive Services
description: Configure a segurança de rede em camadas para seus recursos de serviços cognitivas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: eaffa535b51b786a53f1e6cc35233c55dd837233
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989068"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configurar redes virtuais de Serviços Cognitivos do Azure

Os serviços cognitivas do Azure fornecem um modelo de segurança em camadas. Esse modelo permite que você assegure suas contas de Serviços Cognitivos a um subconjunto específico de redes. Quando as regras de rede são configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes podem acessar a conta. Você pode limitar o acesso a seus recursos com a filtragem de solicitações. Permitir somente solicitações provenientes de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md).

Um aplicativo que acessa um recurso de serviços cognitivas quando as regras de rede estão em vigor requer autorização. Há suporte para autorização com as credenciais do [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ou com uma chave de API válida.

> [!IMPORTANT]
> A ativação de regras de firewall para sua conta de serviços cognitivas bloqueia solicitações de entrada de dados por padrão. Para permitir solicitações por meio do, uma das seguintes condições precisa ser atendida:

> * A solicitação deve originar-se de um serviço operando em uma rede virtual do Azure (VNet) na lista de sub-rede permitida da conta de serviços cognitivas de destino. O ponto de extremidade em solicitações originadas da VNet precisa ser definido como o [subdomínio personalizado](cognitive-services-custom-subdomains.md) de sua conta de serviços cognitivas.
> * Ou a solicitação deve originar-se de uma lista de endereços IP permitidos.
>
> Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger seu recurso de serviços cognitivas, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de Internet) por padrão. Em seguida, você deve configurar regras que permitem acesso ao tráfego de VNets específicas. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos. Você também pode configurar regras para permitir acesso ao tráfego de intervalos de endereço IP de Internet pública selecionados, permitindo conexões de clientes locais ou específicos da Internet.

As regras de rede são impostas em todos os protocolos de rede para serviços cognitivas do Azure, incluindo REST e WebSocket. Para acessar dados usando ferramentas como os consoles de teste do Azure, regras de rede explícitas devem ser configuradas. Você pode aplicar regras de rede a recursos de serviços cognitivas existentes ou ao criar novos recursos de serviços cognitivas. Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações.

## <a name="supported-regions-and-service-offerings"></a>Regiões e ofertas de serviço com suporte

As redes virtuais (VNETs) têm suporte em [regiões em que os serviços cognitivas estão disponíveis](https://azure.microsoft.com/global-infrastructure/services/). Serviços cognitivas dão suporte a marcas de serviço para configuração de regras de rede. Os serviços listados abaixo estão incluídos na marca de serviço **CognitiveServicesManagement** .

> [!div class="checklist"]
> * Detector de Anomalias
> * Pesquisa Visual Computacional
> * Content Moderator
> * Custom Vision
> * Face
> * Reconhecimento de Formulários
> * Leitura Avançada
> * Reconhecimento Vocal (LUIS)
> * Personalizador
> * Serviços de Fala
> * Análise de Texto
> * QnA Maker
> * Tradução de Texto


> [!NOTE]
> Se você estiver usando o LUIS ou os serviços de fala, a marca **CognitiveServicesManagement** só permitirá que você use o serviço usando o SDK ou a API REST. Para acessar e usar o portal do LUIS e/ou o Speech Studio de uma rede virtual, você precisará usar as seguintes marcas:  
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, os recursos de serviços cognitivas aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações nas regras de rede pode afetar a capacidade dos seus aplicativos de se conectar aos serviços cognitivas do Azure. Definir a regra de rede padrão como **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede que **concedam** o acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso. Se você estiver permitindo a listagem de endereços IP para sua rede local, certifique-se de adicionar todos os endereços IP públicos de saída possíveis de sua rede local.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerenciar regras de acesso de rede padrão para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

   ![Opção de rede virtual](media/vnet/virtual-network-blade.png)

1. Para negar o acesso por padrão, opte por permitir o acesso de **Redes selecionadas**. Com a configuração de **redes selecionadas** sozinha, não acompanhada por **redes virtuais** ou **intervalos de endereços** configurados, todo o acesso é efetivamente negado. Quando todo o acesso é negado, as solicitações que tentam consumir o recurso de serviços cognitivas não são permitidas. O portal do Azure, Azure PowerShell ou CLI do Azure ainda pode ser usado para configurar o recurso de serviços cognitivas.
1. Para permitir o tráfego de todas as redes, opte por permitir o acesso de **Todas as redes**.

   ![Negação de redes virtuais](media/vnet/virtual-network-deny.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Exiba o status da regra padrão para o recurso de serviços cognitivas.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Exiba o status da regra padrão para o recurso de serviços cognitivas.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar recursos de serviços cognitivas para permitir o acesso somente de sub-redes específicas. As sub-redes permitidas podem pertencer a uma VNet na mesma assinatura ou em uma assinatura diferente, incluindo assinaturas que pertencem a um locatário de Azure Active Directory diferente.

Habilite um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para serviços cognitivas do Azure na VNet. O ponto de extremidade de serviço roteia o tráfego da VNet por meio de um caminho ideal para o serviço de serviços cognitivas do Azure. As identidades da rede virtual e da sub-rede também são transmitidas com cada solicitação. Os administradores podem configurar as regras de rede para o recurso de serviços cognitivas que permitem que as solicitações sejam recebidas de sub-redes específicas em uma VNet. Os clientes com acesso concedido por essas regras de rede devem continuar atendendo aos requisitos de autorização do recurso de serviços cognitivas para acessar os dados.

Cada recurso de serviços cognitivas dá suporte a até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Permissões necessárias

Para aplicar uma regra de rede virtual a um recurso de serviços cognitivas, o usuário deve ter as permissões apropriadas para as sub-redes que estão sendo adicionadas. A permissão necessária é a função *colaborador* padrão ou a função *colaborador de serviços cognitivas* . As permissões necessárias também podem ser adicionadas às definições de função personalizadas.

O recurso de serviços cognitivas e as redes virtuais com acesso concedido podem estar em assinaturas diferentes, incluindo assinaturas que fazem parte de um locatário diferente do Azure AD.

> [!NOTE]
> A configuração de regras que permitem acesso a sub-redes em redes virtuais que fazem parte de um locatário diferente do Azure Active Directory atualmente só é compatível por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora ele possa exibi-las.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar regras de rede virtual para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma regra de rede existente, em **redes virtuais**, selecione **Adicionar rede virtual existente**.

   ![Adicionar vNet existente](media/vnet/virtual-network-add-existing.png)

1. Selecione as opções **redes virtuais** e **sub-redes** e, em seguida, selecione **habilitar**.

   ![Adicionar detalhes de vNet existentes](media/vnet/virtual-network-add-existing-details.png)

1. Para criar uma nova rede virtual e conceder acesso a ela, selecione **Adicionar nova rede virtual**.

   ![Adicionar nova vNet](media/vnet/virtual-network-add-new.png)

1. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **criar**.

   ![Criar vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se um ponto de extremidade de serviço para serviços cognitivas do Azure não foi configurado anteriormente para a rede virtual e as sub-redes selecionadas, você poderá configurá-lo como parte dessa operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário do Azure Active Directory são mostradas para seleção durante a criação de regras. Para permitir acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

1. Para remover uma rede virtual ou regra de sub-rede, selecione **...** para abrir o menu de contexto da rede virtual ou sub-rede e selecione **remover**.

   ![Remover vNet](media/vnet/virtual-network-remove.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Liste as regras da rede virtual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para serviços cognitivas do Azure em uma rede virtual e sub-rede existentes.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/nome-da-sub-rede".

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Liste as regras da rede virtual.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para serviços cognitivas do Azure em uma rede virtual e sub-rede existentes.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/nome-da-sub-rede".
    > 
    > Você pode usar o parâmetro **subscription** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet

Você pode configurar recursos de serviços cognitivas para permitir o acesso de intervalos de endereços IP de Internet pública específicos. Essa configuração concede acesso a serviços específicos e redes locais, bloqueando efetivamente o tráfego geral da Internet.

Forneça intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formato `16.17.18.0/24` ou como endereços IP individuais como `16.17.18.19` .

   > [!Tip]
   > Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede de IP são permitidas apenas para endereços IP de **Internet pública**. Intervalos de endereços IP reservados para redes privadas (conforme definido em [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos nas regras de IP. Redes privadas incluem endereços que começam com `10.*` , `172.16.*`  -  `172.31.*` e `192.168.*` .

Só há suporte para endereços IPV4 no momento. Cada recurso de serviços cognitivas dá suporte a até 100 regras de rede IP, que podem ser combinadas com [regras de rede virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para o recurso de serviços cognitivas com uma regra de rede IP, você deve identificar os endereços IP voltados para a Internet usados pela sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando o [ExpressRoute](../expressroute/expressroute-introduction.md) local para emparelhamento público ou emparelhamento da Microsoft, você precisará identificar os endereços IP de NAT. Para o emparelhamento público, cada circuito do ExpressRoute, por padrão, usa dois endereços IP NAT. Cada um é aplicado ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT que são usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar regras de rede IP para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a um intervalo IP da Internet, insira o endereço IP ou o intervalo de endereços (no [formato CIDR](https://tools.ietf.org/html/rfc4632)) em intervalo de endereços do **Firewall**  >  . Somente endereços IP públicos válidos (não reservados) são aceitos.

   ![Adicionar intervalo de IP](media/vnet/virtual-network-add-ip-range.png)

1. Para remover uma regra de rede IP, selecione o ícone de lixeira <span class="docon docon-delete x-hidden-focus"></span> ao lado do intervalo de endereços.

   ![Excluir intervalo de IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selecione **Salvar** para salvar suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Liste as regras de rede IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Liste as regras de rede IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="use-private-endpoints"></a>Usar pontos de extremidade privados

Você pode usar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para seus recursos de serviços cognitivas para permitir que clientes em uma rede virtual (VNet) acessem dados com segurança por meio de um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu recurso de serviços cognitivas. O tráfego de rede entre os clientes na VNet e o recurso atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Pontos de extremidade privados para recursos de serviços cognitivas permitem que você:

* Proteja seu recurso de serviços cognitivas Configurando o firewall para bloquear todas as conexões no ponto de extremidade público para o serviço de serviços cognitivas.
* Aumente a segurança para a VNet, permitindo que você bloqueie vazamento de dados da VNet.
* Conecte-se com segurança a recursos de serviços cognitivas de redes locais que se conectam à VNet usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

### <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma interface de rede especial para um recurso do Azure em sua [VNet](../virtual-network/virtual-networks-overview.md). A criação de um ponto de extremidade privado para o recurso de serviços cognitivas fornece conectividade segura entre clientes em sua VNet e seu recurso. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o serviço de serviços cognitivas usa um link privado seguro.

Os aplicativos na VNet podem se conectar ao serviço por meio do ponto de extremidade privado diretamente, usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma. A exceção são os serviços de fala, que exigem um ponto de extremidade separado. Consulte a seção sobre [pontos de extremidade privados com os serviços de fala](#private-endpoints-with-the-speech-services). Pontos de extremidade privados podem ser usados com todos os protocolos compatíveis com o recurso de serviços cognitivas, incluindo REST.

Pontos de extremidade privados podem ser criados em sub-redes que usam [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes em uma sub-rede podem se conectar a um recurso de serviços cognitivas usando o ponto de extremidade privado, ao mesmo tempo que usam pontos de extremidades de serviço para acessar outros.

Quando você cria um ponto de extremidade privado para um recurso de serviços cognitivas em sua VNet, uma solicitação de consentimento é enviada para aprovação para o proprietário do recurso de serviços cognitivas. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário do recurso, essa solicitação de consentimento será aprovada automaticamente.

Os proprietários de recursos de serviços cognitivas podem gerenciar solicitações de consentimento e os pontos de extremidade privados, por meio da guia '*pontos de extremidade particulares*' para o recurso de serviços cognitivas no [portal do Azure](https://portal.azure.com).

### <a name="private-endpoints"></a>Pontos de extremidade privados

Ao criar o ponto de extremidade privado, você deve especificar o recurso de serviços cognitivas ao qual ele se conecta. Para obter mais informações sobre como criar um ponto de extremidade privado, consulte:

* [Criar um ponto de extremidade privado usando o centro de links privado no portal do Azure](../private-link/create-private-endpoint-portal.md)
* [Criar um ponto de extremidade privado usando a CLI do Azure](../private-link/create-private-endpoint-cli.md)
* [Criar um ponto de extremidade privado usando o Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Conectando-se a pontos de extremidade privados

Os clientes em uma VNet usando o ponto de extremidade privado devem usar a mesma cadeia de conexão para o recurso de serviços cognitivas como clientes que se conectam ao ponto de extremidade público. A exceção são os serviços de fala, que exigem um ponto de extremidade separado. Consulte a seção sobre [pontos de extremidade privados com os serviços de fala](#private-endpoints-with-the-speech-services). Nós confiamos na resolução DNS para rotear automaticamente as conexões da VNet para o recurso de serviços cognitivas por meio de um link privado. 

Criamos uma [zona DNS privada](../dns/private-dns-overview.md) anexada à VNet com as atualizações necessárias para os pontos de extremidade privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS. A seção sobre o [DNS é alterada](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para pontos de extremidade privados.

### <a name="private-endpoints-with-the-speech-services"></a>Pontos de extremidade privados com os serviços de fala

Consulte [usando os serviços de fala com pontos de extremidade privados fornecidos pelo link privado do Azure](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares

Quando você cria um ponto de extremidade privado, o registro de recurso DNS CNAME para o recurso de serviços cognitivas é atualizado para um alias em um subdomínio com o prefixo '*privatelink*'. Por padrão, também criamos uma [zona DNS privada](../dns/private-dns-overview.md), correspondente ao subdomínio '*privatelink*', com os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve a URL do ponto de extremidade de fora da VNet com o ponto de extremidade privado, ela é resolvida para o ponto de extremidade público do recurso de serviços cognitivas. Quando resolvido da VNet que hospeda o ponto de extremidade privado, a URL do ponto de extremidade é resolvida para o endereço IP do ponto de extremidade privado.

Essa abordagem permite o acesso ao recurso de serviços cognitivas usando a mesma cadeia de conexão para clientes na VNet que hospedam os pontos de extremidade privados e clientes fora da VNet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes deverão ser capazes de resolver o FQDN (nome de domínio totalmente qualificado) para o ponto de extremidade de recurso dos serviços cognitivas para o endereço IP do ponto de extremidade privado. Configure o servidor DNS para delegar seu subdomínio de vínculo privado à zona DNS privada para a VNet.

> [!TIP]
> Ao usar um servidor DNS local ou personalizado, você deve configurar o servidor DNS para resolver o nome do recurso de serviços cognitivas no subdomínio ' privatelink ' para o endereço IP do ponto de extremidade privado. Você pode fazer isso delegando o subdomínio ' privatelink ' à zona DNS privada da VNet ou configurando a zona DNS no servidor DNS e adicionando os registros DNS A.

Para obter mais informações sobre como configurar seu próprio servidor DNS para dar suporte a pontos de extremidade privados, consulte os seguintes artigos:

* [Resolução de nomes para recursos em redes virtuais do Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [Configuração de DNS para pontos de extremidade privados](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Próximas etapas

* Explore os vários [Serviços cognitivas do Azure](./what-are-cognitive-services.md)
* Saiba mais sobre os [pontos de extremidade de serviço de rede virtual do Azure](../virtual-network/virtual-network-service-endpoints-overview.md)
