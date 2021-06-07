---
title: Criar, alterar ou excluir um emparelhamento de rede virtual do Azure | Microsoft Docs
description: Criar, alterar ou excluir um emparelhamento de rede virtual. Com o emparelhamento de rede virtual, você conecta redes virtuais na mesma região e entre regiões.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222659"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou excluir um emparelhamento da rede virtual

Saiba como criar, alterar ou excluir um emparelhamento de rede virtual. O peering de rede virtual permite que você conecte redes virtuais na mesma região e entre regiões (também conhecido como Global VNet Peering) por meio da rede do backbone do Azure. Depois de emparelhadas, as duas redes virtuais ainda são gerenciadas como recursos separados. Se você é novo em emparelhamento de redes virtuais, pode saber mais sobre isso na [visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) ou concluindo um [tutorial](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.come faça logon com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com emparelhamentos.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` com uma conta que tenha [permissões necessárias](#permissions) para trabalhar com o emparelhamento, para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando a CLI do Azure localmente, também precisará executar `az login` com uma conta que tenha [permissões necessárias](#permissions) para trabalhar com o emparelhamento, para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-peering"></a>Criar um emparelhamento

Antes de criar um emparelhamento, familiarize-se com os requisitos e restrições e as [permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior do Portal do Azure, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção. Não selecione **redes virtuais (clássicas)** se for exibido na lista, pois não é possível criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione na lista a rede virtual para a qual você deseja criar um emparelhamento.
3. Em **CONFIGURAÇÕES**, selecione **Emparelhamentos**.
4. Selecione **+ Adicionar**. 
5. <a name="add-peering"></a>Insira ou selecione valores para as seguintes configurações:
    - **Nome:** O nome para o emparelhamento deve ser exclusivo na rede virtual.
    - **Modelo de implantação de rede virtual:** Selecione com qual modelo de implantação a rede virtual que você deseja emparelhar foi implantada.
    - **Sei minha ID de recurso:** Se você tem acesso de leitura para a rede virtual que você deseja emparelhar, deixe essa caixa de seleção desmarcada. Se você não tiver acesso de leitura para a rede virtual ou a assinatura que você deseja emparelhar, marque essa caixa de seleção. Insira a ID de recurso completo da rede virtual que você deseja emparelhar na caixa **ID de Recurso** exibida quando você tiver marcado a caixa. A ID de recurso que você digitar deverá ser para uma rede virtual existente na mesma [região](https://azure.microsoft.com/regions) ou região [diferente com suporte](#requirements-and-constraints) do Azure que essa rede virtual. A ID do recurso completo é semelhante a `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . Você pode obter a ID de recurso para uma rede virtual ao exibir as propriedades de uma rede virtual. Para saber como exibir as propriedades de uma rede virtual, confira [Gerenciar as redes virtuais](manage-virtual-network.md#view-virtual-networks-and-settings). Se a assinatura estiver associada a um locatário do Azure Active Directory diferente da assinatura com a rede virtual da qual você está criando o emparelhamento, primeiro adicione um usuário de cada locatário como um [usuário convidado](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no locatário oposto.
    - **Assinatura:** Selecione a [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual que você deseja emparelhar. Uma ou mais assinaturas estão listadas, dependendo de quantas assinaturas sua conta tem acesso de leitura. Se você tiver marcado a caixa de seleção **ID de Recurso**, essa configuração não estará disponível.
    - **Rede virtual:** Selecione a rede virtual que você deseja emparelhar. Você pode selecionar uma rede virtual criada por meio de qualquer um dos modelos de implantação do Azure. Se você deseja selecionar uma rede virtual em uma região diferente, deve selecionar uma rede virtual em um [região com suporte](#cross-region). Você deve ter acesso de leitura para a rede virtual para que ela fique visível na lista. Se uma rede virtual estiver listada, mas esmaecida, pode ser porque o espaço de endereço para a rede virtual se sobrepõe ao espaço de endereço para essa rede virtual. Se os espaços de endereço de rede virtual se sobrepõem, eles não podem ser emparelhados. Se você tiver marcado a caixa de seleção **ID de Recurso**, essa configuração não estará disponível.
    - **Permitir acesso à rede virtual:** Selecione **habilitado** (padrão) se você quiser habilitar a comunicação entre as duas redes virtuais por meio do `VirtualNetwork` fluxo padrão. Habilitar a comunicação entre redes virtuais permite que os recursos conectados a qualquer rede virtual se comuniquem entre si com a mesma largura de banda e latência como se estivessem conectados à mesma rede virtual. Todas as comunicações entre os recursos nas duas redes virtuais estão na rede privada do Azure. A marca de serviço do **VirtualNetwork** para grupos de segurança de rede abrange a rede virtual e a rede virtual emparelhada quando essa configuração está **habilitada**. (Para saber mais sobre as marcas de serviço do grupo de segurança de rede, consulte [visão geral dos grupos de segurança de rede](./network-security-groups-overview.md#service-tags).) Selecione **desabilitado** se você não quiser que o tráfego flua para a rede virtual emparelhada por padrão. Você poderá selecionar **desabilitado** se tiver emparelhado uma rede virtual com outra rede virtual, mas, ocasionalmente, quiser desabilitar o fluxo de tráfego padrão entre as duas redes virtuais. Você pode achar que a habilitação e desabilitação é mais conveniente que excluir e recriar emparelhamentos. Quando essa configuração é desabilitada, o tráfego não flui entre as redes virtuais emparelhadas por padrão; no entanto, o tráfego ainda pode fluir explicitamente por meio de uma regra de [grupo de segurança de rede](./network-security-groups-overview.md) que inclui os endereços IP apropriados ou grupos de segurança de aplicativo.
      > [!WARNING]
      > Desabilitar a configuração **permitir acesso à rede virtual** altera apenas a definição da marca de serviço **VirtualNetwork** . Ele *não* impede totalmente o fluxo de tráfego pela conexão de mesmo nível, conforme explicado nesta descrição de configuração.    
    - **Permitir trafego encaminhado:** marque essa caixa para permitir que o tráfego *encaminhado* por um dispositivo virtual de rede em uma rede virtual (que não tenha como origem a rede virtual) flua para essa rede virtual por meio de um emparelhamento. Por exemplo, considere três redes virtuais chamadas Spoke1, Spoke2 e Hub. Existe um emparelhamento entre cada rede virtual spoke e a rede virtual hub, mas não existem emparelhamentos entre as redes virtuais spoke. Um dispositivo de rede virtual é implantado na rede virtual Hub e as rotas definidas pelo usuário são aplicadas a cada rede virtual Spoke que roteia tráfego entre as sub-redes por meio do dispositivo de rede virtual. Se essa caixa de seleção não for verificada para o emparelhamento entre cada rede virtual do spoke e a rede virtual do Hub, o tráfego não fluirá entre as redes virtuais do spoke porque o Hub não está encaminhando o tráfego entre as redes virtuais. No entanto habilitar esta funcionalidade permite o tráfego encaminhado por meio do emparelhamento, não cria nenhuma rota definida pelo usuário ou dispositivo de rede virtual. Rotas definidas pelo usuário e dispositivos de rede virtual são criados separadamente. Saiba mais sobre [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Não será necessário verificar essa configuração, se o tráfego for encaminhado entre redes virtuais por meio de um Gateway de VPN do Azure.
    - **Permitir tráfego de gateway:** Marque essa caixa de seleção se você tiver um gateway de rede virtual conectado a essa rede virtual e deseja permitir o tráfego de rede virtual emparelhado para fluir através do gateway. Por exemplo, esta rede virtual pode ser anexada a uma rede local por meio de um gateway de rede virtual. O gateway pode ser um gateway de VPN ou de ExpressRoute. Marcar essa caixa permite que o tráfego da rede virtual emparelhada flua pelo gateway anexado a esta rede virtual até a rede local. Se você marcar essa caixa, a rede virtual emparelhada não pode ter um gateway configurado. A rede virtual emparelhada deve ter a caixa de seleção **Usar gateways remotos** marcada ao configurar o emparelhamento de outra rede virtual para esta rede virtual. Se você deixar essa caixa desmarcada (padrão), o tráfego da rede virtual emparelhada continuará fluindo para esta rede virtual, mas não poderá fluir através de um gateway de rede virtual conectado a essa rede virtual. Se o emparelhamento estiver entre uma rede virtual (Gerenciador de Recursos) e uma rede virtual (clássica), o gateway deve estar na rede virtual (Gerenciador de Recursos).

       Além de encaminhar o tráfego para uma rede local, um gateway de VPN pode encaminhar o tráfego de rede entre as redes virtuais emparelhadas com a rede virtual em que o gateway está, sem precisar que as redes virtuais sejam emparelhadas umas com as outras. Usar um gateway VPN para encaminhar o tráfego é útil quando você deseja usar um gateway de VPN em uma rede virtual de hub (consulte o exemplo de hub e de spoke descrito para **Permitir o tráfego encaminhado**) para rotear o tráfego entre as redes virtuais de spoke não emparelhadas umas com as outras. Para saber mais sobre como permitir o uso de um gateway para trânsito, consulte [Configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Esse cenário requer a implementação de rotas definidas pelo usuário que especificam o gateway de rede virtual como o tipo de próximo de salto. Saiba mais sobre [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Você só pode especificar um gateway de VPN como o próximo tipo de salto em uma rota definida pelo usuário, não é possível especificar um gateway de ExpressRoute como o próximo tipo de salto em uma rota definida pelo usuário.

    - **Usar gateway remotos:** Marque essa caixa de seleção para permitir o tráfego desta rede virtual fluir por meio de um gateway de rede virtual conectado a uma rede virtual que você estiver emparelhando. Por exemplo, a rede virtual que você está emparelhando tem um gateway de VPN anexado que permite a comunicação com uma rede local.  Marcar essa caixa permite que o tráfego da rede virtual flua por meio do gateway de VPN conectado à rede virtual emparelhada. Se você marcar essa caixa, a rede virtual emparelhada deve ter um gateway de rede virtual anexado a ela e deve ter a caixa de seleção **Permitir tráfego de gateway** marcada. Se você deixar essa caixa desmarcada (padrão), o tráfego da rede virtual emparelhada poderá continuar fluindo para esta rede virtual, mas não poderá fluir através de um gateway de rede virtual conectado a essa rede virtual.
    
      Apenas um emparelhamento para essa rede virtual pode ter essa configuração habilitada.

      Você não poderá usar gateways remotos se já tiver um gateway configurado em sua rede virtual. Para saber mais sobre como usar um gateway para trânsito, consulte [Configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Se você usar um gateway de rede virtual para enviar o tráfego local transitivamente para uma VNet emparelhada, o intervalo de IP de VNet emparelhado para o dispositivo VPN local deverá ser definido como tráfego "interessante". Caso contrário, seus recursos locais não poderão se comunicar com os recursos na VNet emparelhada.

6. Selecione **OK** para adicionar o emparelhamento à rede virtual que você selecionou.

Para obter instruções passo a passo para a implementação de emparelhamento entre redes virtuais em diferentes assinaturas e modelos de implantação, consulte [próximas etapas](#next-steps).

### <a name="commands"></a>Comandos

- **CLI do Azure**: [AZ Network vnet emparelhamento criar](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Exibir ou alterar as configurações de emparelhamento

Antes de alterar um emparelhamento, familiarize-se com os requisitos e restrições e as [permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção. Não selecione **redes virtuais (clássicas)** se for exibido na lista, pois não é possível criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione na lista a rede virtual para a qual você deseja alterar as configurações de emparelhamento.
3. Em **CONFIGURAÇÕES**, selecione **Emparelhamentos**.
4. Selecione o emparelhamento que você deseja exibir ou alterar as configurações.
5. Altere a configuração apropriada. Leia sobre as opções para cada configuração na [etapa 5](#add-peering) de criar um emparelhamento.
6. Clique em **Salvar**.

**Comandos**

- **CLI do Azure**: [AZ Network vnet emparelhamento List](/cli/azure/network/vnet/peering) para listar emparelhamentos para uma rede virtual, [AZ Network vnet emparelhamento](/cli/azure/network/vnet/peering) para mostrar as configurações de um emparelhamento específico e [AZ Network vnet peering Update](/cli/azure/network/vnet/peering) para alterar as configurações de emparelhamento. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) para recuperar as configurações de emparelhamento de exibição e [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) para alterar as configurações.

## <a name="delete-a-peering"></a>Excluir um emparelhamento

Antes de excluir um emparelhamento, certifique-se de que sua conta tenha as [permissões necessárias](#permissions).

Quando um emparelhamento é excluído, o tráfego de uma rede virtual não flui mais para a rede virtual emparelhada. Quando as redes virtuais implantadas por meio do Resource Manager são emparelhadas, cada rede virtual tem um emparelhamento para a outra rede virtual. Embora excluindo o emparelhamento de uma rede virtual desabilita a comunicação entre as redes virtuais, ele não exclui o emparelhamento de outra rede virtual. O status de emparelhamento para o emparelhamento existente em outra rede virtual é **Desconectado**. Você não pode recriar o emparelhamento até que recrie o emparelhamento na primeira rede virtual e o status de emparelhamento para ambas as redes virtuais alterar para *Conectado*.

Se você deseja que as redes virtuais se comuniquem, às vezes, mas não sempre, em vez de excluir um emparelhamento, você pode definir a configuração **Permitir acesso à rede virtual** para **desabilitado**. Para saber como, leia a etapa 6 da seção Criar um emparelhamento deste artigo. Você pode achar que desabilitar e habilitar o acesso à rede é mais fácil do que excluir e recriar emparelhamentos.

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção. Não selecione **redes virtuais (clássicas)** se for exibido na lista, pois não é possível criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione na lista a rede virtual para a qual você deseja excluir um emparelhamento.
3. Em **CONFIGURAÇÕES**, selecione **Emparelhamentos**.
4. No lado direito do emparelhamento que deseja excluir, selecione **...**, selecione **Excluir** e, em seguida, selecione **Sim** para excluir o emparelhamento da primeira rede virtual.
5. Conclua as etapas anteriores para excluir o emparelhamento de outra rede virtual no emparelhamento.

**Comandos**

- **CLI do Azure**: [AZ Network vnet emparelhamento Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos e restrições

- <a name="cross-region"></a>Você pode parear redes virtuais na mesma região ou em regiões diferentes. O emparelhamento de redes virtuais em regiões diferentes também é conhecido como *emparelhamento VNet global*. 
- Ao criar um emparelhamento global, as redes virtuais emparelhadas podem existir em qualquer região da nuvem pública do Azure ou regiões de nuvem da China ou regiões de nuvem do governo. Você não pode emparelhar entre nuvens. Por exemplo, uma VNet na nuvem pública do Azure não pode ser emparelhada com uma VNet na nuvem do Azure na China.
- Os recursos em uma rede virtual não podem se comunicar com o endereço IP de front-end de um balanceador de carga interno básico em uma rede virtual emparelhada globalmente. O suporte para o balanceador de carga básico só existe dentro da mesma região. O suporte para o Standard Load Balancer existe tanto para o Emparelhamento VNet quanto para o Emparelhamento VNet Global. Os serviços que usam um balanceador de carga básico que não funcionará em emparelhamento VNet global são documentados [aqui.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Você pode usar gateways remotos ou permitir o trânsito de gateway em redes virtuais emparelhadas globalmente e em redes virtuais emparelhadas localmente.
- As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes. Ao usar redes virtuais em diferentes assinaturas, ambas as assinaturas podem ser associadas ao mesmo locatário ou a um locatário diferente do Azure Active Directory. Se você ainda não tiver um locatário do AD, poderá [criar um](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- As redes virtuais que você emparelhar devem ter espaços de endereço IP não sobrepostos.
- Você não pode adicionar ou excluir intervalos de endereços do espaço de endereço de uma rede virtual após ela ser emparelhada com outra rede virtual. Para adicionar ou remover intervalos de endereço, exclua o emparelhamento, adicione ou remova os intervalos de endereço e, em seguida, recrie o emparelhamento. Para adicionar ou remover intervalos de endereços de redes virtuais, consulte [Gerenciar redes virtuais](manage-virtual-network.md).
- Você pode emparelhar duas redes virtuais implantadas por meio do Resource Manager ou uma rede virtual implantado por meio do Resource Manager com uma rede virtual implantada por meio do modelo de implantação clássico. Não é possível emparelhar duas redes virtuais criadas por meio do modelo de implantação clássico. Se você não estiver familiarizado com os modelos de implantação do Azure, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode usar um [Gateway de VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar duas redes virtuais criadas usando o modelo de implantação clássico.
- Ao emparelhar duas redes virtuais criadas por meio do Resource Manager, um emparelhamento deve ser configurado para cada rede virtual no emparelhamento. Você verá um dos seguintes tipos de status de emparelhamento: 
  - *Iniciado em:* Quando você cria o emparelhamento para a segunda rede virtual da primeira rede virtual, o status de emparelhamento é *iniciado*. 
  - *Conectado:* Quando você cria o emparelhamento da segunda rede virtual para a primeira rede virtual, seu status de emparelhamento é *conectado*. Se você exibir o status de emparelhamento para a primeira rede virtual, verá seu status alterado de *iniciado* para *conectado*. O emparelhamento não é estabelecido com êxito até que o status de emparelhamento de ambos os emparelhamentos de rede virtual esteja *conectado*.
- Ao emparelhar duas redes virtuais criadas por meio do Resource Manager com uma rede virtual criada por meio do modelo de implantação clássico, você configura apenas um emparelhamento para a rede virtual implantada por meio do Resource Manager. Não é possível configurar o emparelhamento de uma rede virtual (clássica) ou entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Quando você cria o emparelhamento de rede virtual (Resource Manager) para a rede virtual (clássica), o status de emparelhamento é *Atualizando*, em seguida altera rapidamente para *Conectado*.
- Um emparelhamento é estabelecido entre duas redes virtuais. Os emparelhamentos por si só não são transitivos. Se você criar emparelhamentos entre:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Não há nenhum emparelhamento entre VirtualNetwork1 e VirtualNetwork3 por meio de VirtualNetwork2. Se você quiser criar um emparelhamento de rede virtual entre VirtualNetwork1 e VirtualNetwork3, precisará criar um emparelhamento entre VirtualNetwork1 e VirtualNetwork3. Não há nenhum emparelhamento entre VirtualNetwork1 e VirtualNetwork3 por meio de VirtualNetwork2. Se você quiser que o VirtualNetwork1 e o VirtualNetwork3 se comuniquem diretamente, será necessário criar um emparelhamento explícito entre VirtualNetwork1 e VirtualNetwork3 ou passar por um NVA na rede de Hub.  
- Você não pode resolver nomes em redes virtuais emparelhadas usando a resolução de nomes do Azure padrão. Para resolver nomes em outras redes virtuais, você deve usar o [DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um servidor DNS personalizado. Para saber como configurar seu próprio servidor DNS, consulte [Resolução de nome usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Recursos em redes virtuais emparelhadas na mesma região podem se comunicar entre si com a mesma largura de banda e latência como se estivessem na mesma rede virtual. No entanto, o tamanho de cada máquina virtual tem sua própria largura de banda de rede máxima. Para saber mais sobre a largura de banda de rede máxima para diferentes tamanhos de máquina virtual, consulte os tamanhos de máquina virtual do [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Uma rede virtual pode ser emparelhada a outra rede virtual e também estar conectada a outra rede virtual com um gateway de rede virtual do Azure. Quando as redes virtuais são conectadas por meio do emparelhamento e um gateway, fluxos de tráfego entre as redes virtuais por meio da configuração de emparelhamento, em vez do gateway.
- Os clientes VPN ponto a site devem ser baixados novamente após o emparelhamento da rede virtual ter sido configurado com êxito para garantir que as novas rotas sejam baixadas para o cliente.
- Há um custo nominal para tráfego de entrada e saída que utiliza um emparelhamento de rede virtual. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Permissões

As contas que você pode usar para trabalhar com o emparelhamento de rede virtual devem ser atribuídas às seguintes funções:

- [Colaborador de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): para uma rede virtual implantada por meio do Gerenciador de Recursos.
- [Colaborador de Rede Clássico](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): para uma rede virtual implantada por meio do modelo de implantação clássico.

Se sua conta não está atribuída a uma das funções anteriores, ela deve ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe atribuição para as ações necessárias da tabela a seguir:

| Ação                                                          | Nome |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessário para criar um emparelhamento de a rede virtual A com a rede virtual B. A rede virtual A um deve ser uma rede virtual (Gerenciador de Recursos)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessário para criar um emparelhamento de rede virtual B (Gerenciador de Recursos) com a rede virtual A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Necessário para criar um emparelhamento de rede virtual B (clássica) com a rede virtual A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Ler um emparelhamento de rede virtual   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um emparelhamento de rede virtual |

## <a name="next-steps"></a>Próximas etapas

- Um emparelhamento de rede virtual é criado entre redes virtuais criadas com modelos de implantação iguais ou diferentes que existem nas mesmas assinaturas ou em assinaturas diferentes. Conclua um tutorial para um dos seguintes cenários:

  |Modelo de implantação do Azure             | Subscription  |
  |---------                          |---------|
  |Ambos Resource Manager              |[Idêntico](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diferente](create-peering-different-subscriptions.md)|
  |Um Resource Manager, um clássico  |[Idêntico](create-peering-different-deployment-models.md)|
  |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

- Saiba como criar uma [topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar um emparelhamento de rede virtual usando [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para redes virtuais