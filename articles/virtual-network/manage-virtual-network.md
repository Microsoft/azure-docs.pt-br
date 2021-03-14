---
title: Criar, alterar ou excluir uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Crie e exclua uma rede virtual e altere as configurações, como servidores DNS e espaços de endereço IP, para uma rede virtual existente.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 1ec4e26e266a1777857fe0e890b093f60a3597f0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612517"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou excluir uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como criar e excluir uma rede virtual e alterar as configurações, como servidores DNS e espaços de endereço IP para uma rede virtual existente. Se você é novo em redes virtuais, aprenda mais sobre eles no [visão geral de Rede Virtual](virtual-networks-overview.md) ou executando um [tutorial](quick-create-portal.md). Uma rede virtual contém sub-redes. Para saber como criar, alterar e excluir sub-redes, consulte [Gerenciar sub-redes](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.
- A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ criar um recurso**  >    >  **rede redes virtuais**.
2. Insira ou selecione valores para as seguintes configurações e, em seguida, selecione **criar**:
   - **Nome**: O nome deve ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) em que você optar por criar a rede virtual. Você não pode alterar o nome depois de criar a rede virtual. Você pode criar várias redes virtuais ao longo do tempo. Para sugestões de nomenclaturas, consulte [Convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Seguir uma convenção de nomenclatura pode ajudar a tornar mais fácil de gerenciar várias redes virtuais.
   - **Espaço de endereço**: o espaço de endereço de uma rede virtual é composto por um ou mais intervalos de endereços não sobrepostos especificados na notação CIDR. O intervalo de endereços definido pode ser público ou privado (RFC 1918). Se você definir o intervalo de endereços como público ou privado, o intervalo de endereços será acessível somente de dentro da rede virtual, de redes virtuais interconectadas e de quaisquer redes locais que você se conectou à rede virtual. Não é possível adicionar os seguintes intervalos de endereços:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Broadcast)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 (DNS Interno, DHCP e [investigação de integridade](../load-balancer/load-balancer-custom-probe-overview.md#probesource) do Azure Load Balancer)

     Embora você possa definir apenas um intervalo de endereços ao criar a rede virtual no portal, é possível adicionar mais intervalos de endereços ao espaço de endereço depois que a rede virtual é criada. Para saber como adicionar um intervalo de endereços a uma rede virtual existente, consulte [Adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).

     > [!WARNING]
     > Se uma rede virtual tiver intervalos de endereços que se sobrepõem a outra rede virtual ou rede local, as duas redes não poderão ser conectadas. Antes de definir um intervalo de endereços, considere se você deseja conectar a rede virtual a outras redes virtuais ou redes locais no futuro. A Microsoft recomenda a configuração de intervalos de endereços de rede virtual com espaço de endereço privado ou espaço de endereço público de propriedade de sua organização.
     >

     - **Nome da sub-rede**: O nome da sub-rede deve ser exclusivo na rede virtual. Você não pode alterar o nome da sub-rede após a criação da sub-rede. O portal requer que você defina uma sub-rede ao criar uma rede virtual, mesmo que uma rede virtual não precise ter nenhuma sub-rede. No portal, você pode definir uma ou mais sub-redes ao criar uma rede virtual. Você pode adicionar mais sub-redes na rede virtual mais tarde, depois de criar a rede virtual. Para adicionar uma sub-rede a uma rede virtual, consulte [Gerenciar sub-redes](virtual-network-manage-subnet.md). Você pode criar uma rede virtual que tem várias sub-redes usando a CLI do Azure ou o PowerShell.

       >[!TIP]
       >Às vezes, administradores criam sub-redes diferentes para filtrar ou controlar o roteamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como você deseja filtrar e rotear o tráfego entre suas sub-redes. Para saber mais sobre a filtragem de tráfego entre sub-redes, consulte [Grupos de segurança de rede](./network-security-groups-overview.md). O Azure faz o roteamento de tráfego entre as sub-redes automaticamente, mas você pode substituir as rotas padrão do Azure. Para saber mais sobre o roteamento de tráfego de sub-rede padrão do Azure, consulte [Visão geral do roteamento](virtual-networks-udr-overview.md).
       >

     - **Intervalo de endereços de sub-rede:** O intervalo deve estar dentro do espaço de endereço inserido para a rede virtual. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, uma rede virtual com um intervalo de endereços de sub-rede de /29 tem apenas três endereços IP utilizáveis. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Gerenciar sub-redes](virtual-network-manage-subnet.md).
     - **Assinatura**: selecione uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Você não pode usar a mesma rede virtual em mais de uma assinatura do Azure. No entanto, você pode conectar uma rede virtual em uma assinatura a redes virtuais em outras assinaturas com o [emparelhamento de rede virtual](virtual-network-peering-overview.md). Qualquer recurso do Azure que você conectar à rede virtual deve ser na mesma assinatura da rede virtual.
     - **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Um recurso do Azure que você conecta à rede virtual pode ser no mesmo grupo de recursos da rede virtual ou em grupo de recursos diferente.
     - **Local:** Selecione um [local](https://azure.microsoft.com/regions/) do Azure, também conhecido como região. Uma rede virtual pode estar em um só local do Azure. No entanto, você pode conectar uma rede virtual em um local para uma rede virtual em outro local usando um gateway de VPN. Qualquer recurso do Azure que você conectar à rede virtual deve ser no mesmo local da rede virtual.

**Comandos**

- CLI do Azure: [az network vnet create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Exibir configurações e redes virtuais

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual para a qual deseja exibir as configurações.
3. As configurações a seguir são listadas para a rede virtual selecionada:
   - **Visão geral:** Fornece informações sobre a rede virtual, incluindo espaço de endereço e servidores DNS. A captura de tela a seguir mostra as configurações de visão geral de uma rede virtual chamada **MyVNet**:

     ![Visão geral da interface de rede](./media/manage-virtual-network/vnet-overview.png)

     Você pode mover uma rede virtual para um grupo de recursos ou assinatura diferente selecionando **Alterar** ao lado de **Grupo de recursos** ou **Nome da assinatura**. Para saber como mover uma rede virtual, consulte [Mover recursos para um outro grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure. Todos os recursos que estão conectados à rede virtual devem mover-se com a rede virtual.
   - **Espaço de endereço:** Os espaços de endereço que são atribuídos à rede virtual são listados. Para saber como adicionar e remover um intervalo de endereços do espaço de endereço, conclua as etapas em [Adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).
   - **Dispositivos conectados**: Todos os recursos que estão conectados à rede virtual são listados. Na captura de tela anterior, três interfaces de rede e um balanceador de carga são conectados à rede virtual. Os novos recursos que você criar e conectar à rede virtual são listados. Se você excluir um recurso que foi conectado à rede virtual, ele não aparecerá mais na lista.
   - **Sub-redes:** Uma lista de sub-redes que existem na rede virtual é exibida. Para saber como adicionar e remover uma sub-rede, consulte [Gerenciar sub-redes](virtual-network-manage-subnet.md).
   - **Servidores DNS:** Você pode especificar se o servidor DNS interno do Azure ou o servidor DNS personalizado fornece a resolução de nome para dispositivos que estão conectados à rede virtual. Quando você cria uma rede virtual usando o portal do Azure, os servidores DNS do Azure são usados para resolução de nomes em uma rede virtual por padrão. Para modificar os servidores DNS, conclua as etapas em [Alterar servidores DNS](#change-dns-servers) deste artigo.
   - **Emparelhamentos:** Se houver emparelhamentos existentes na assinatura, eles serão listados aqui. Você pode exibir configurações para emparelhamentos existentes, ou criar, alterar ou excluir emparelhamentos. Para saber mais sobre emparelhamento, consulte [Emparelhamento de rede virtual](virtual-network-peering-overview.md).
   - **Propriedades:** Exibe configurações sobre a rede virtual, incluindo a ID do recurso da rede virtual e a assinatura em que ela está.
   - **Diagrama:** O diagrama apresenta uma representação visual de todos os dispositivos que estão conectados à rede virtual. O diagrama tem algumas informações importantes sobre os dispositivos. Para gerenciar um dispositivo nesta exibição, no diagrama, selecione o dispositivo.
   - **Configurações comuns do Azure**: Para saber mais sobre configurações comuns do Azure, consulte as seguintes informações:
     - [Log de atividades](../azure-monitor/essentials/platform-logs-overview.md)
     - [Controle de acesso (IAM)](../role-based-access-control/overview.md)
     - [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Script de automação](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- CLI do Azure: [az network vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adicionar ou remover um intervalo de endereços

Você pode adicionar e remover intervalos de endereços para uma rede virtual. Um intervalo de endereços deve ser especificado na notação CIDR e não pode se sobrepor a outros intervalos de endereços dentro da mesma rede virtual. Os intervalos de endereços definidos podem ser públicos ou privados (RFC 1918). Se você definir o intervalo de endereços como público ou privado, o intervalo de endereços será acessível somente de dentro da rede virtual, de redes virtuais interconectadas e de quaisquer redes locais que você se conectou à rede virtual. 

Você pode diminuir o intervalo de endereços para uma rede virtual, contanto que ela ainda inclua os intervalos de quaisquer sub-redes associadas. Além disso, você pode estender o intervalo de endereços, por exemplo, alterando um/16 para/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Não é possível adicionar os seguintes intervalos de endereços:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Broadcast)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (DNS Interno, DHCP e [investigação de integridade](../load-balancer/load-balancer-custom-probe-overview.md#probesource) do Azure Load Balancer)

Para adicionar ou remover um intervalo de endereços:

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual para a qual deseja adicionar ou remover um intervalo de endereços.
3. Selecione **Espaço de endereço** em **CONFIGURAÇÕES**.
4. Preencha uma das seguintes opções:
    - **Adicionar um intervalo de endereços:** digite o novo intervalo de endereços. O intervalo de endereços não pode se sobrepor a um intervalo de endereços existente definido para a rede virtual.
    - **Remover um intervalo de endereços**: à direita do intervalo de endereços que deseja remover, selecione **...** e, em seguida, selecione **Remover**. Se existir uma sub-rede no intervalo de endereços, você não poderá removê-lo. Para remover um intervalo de endereços, você deve primeiro excluir todas as sub-redes (e outros recursos nas sub-redes) existentes no intervalo de endereços.
5. Selecione **Salvar**.

**Comandos**

- CLI do Azure: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Alterar os servidores DNS

Todas as VMs que estão conectadas ao registro da rede virtual com os servidores DNS que você especificar para a rede virtual. Eles também usam o servidor DNS especificado para resolução de nome. Cada NIC (interface de rede) em uma VM pode ter suas próprias configurações do servidor DNS. Se uma NIC tiver suas próprias configurações de servidor DNS, elas substituirão as configurações do servidor DNS para a rede virtual. Para saber mais sobre as configurações de DNS da NIC, consulte [Configurações e tarefas da interface de rede](virtual-network-network-interface.md#change-dns-servers). Para saber mais sobre a resolução de nomes para VMs e instâncias de função de Serviços de Nuvem do Azure, consulte [Resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual para a qual deseja alterar os servidores DNS.
3. Selecione **servidores DNS**, em **configurações**.
4. Selecione uma das seguintes opções:
   - **Padrão (fornecido pelo Azure)**: todos os nomes de recursos e endereços IP privados são registrados automaticamente nos servidores DNS do Azure. Você pode resolver nomes entre quaisquer recursos que estejam conectados à mesma rede virtual. Você não pode usar essa opção para resolver nomes entre redes virtuais. Para resolver nomes em outras redes virtuais, você deve usar um servidor DNS personalizado.
   - **Personalizado:** você pode adicionar um ou mais servidores, até o limite do Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Você tem as seguintes opções:
   - **Adicionar um endereço**: Adiciona o servidor à sua lista de servidores DNS da rede virtual. Essa opção também registra o servidor DNS com o Azure. Se você já tiver registrado um servidor DNS com o Azure, poderá selecionar esse servidor DNS na lista.
   - **Remover um endereço:** Ao lado do servidor que deseja remover, selecione **...** e, em seguida, **Remover**. Excluir o servidor remove o servidor somente dessa lista de rede virtual. O servidor DNS permanece registrado no Azure para que suas outras redes virtuais possam usá-lo.
   - **Reordenar endereços do servidor DNS**: é importante verificar se os servidores DNS estão listados na ordem correta para seu ambiente. As listas de servidores DNS são usadas na ordem em que foram especificadas. Eles não funcionam como uma configuração de round-robin. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS, não importa se ele está funcionando corretamente. Remova todos os servidores DNS listados e, em seguida, adicione-os de volta na ordem que desejar.
   - **Alterar um endereço**: destaque o servidor DNS na lista, e em seguida digite o novo endereço.
5. Selecione **Salvar**.
6. Reinicie as VMs que estão conectadas à rede virtual, para que as novas configurações do servidor DNS sejam atribuídas a elas. As VMs continuam a usar suas configurações de DNS atuais até que elas sejam reiniciadas.

**Comandos**

- CLI do Azure: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Excluir uma rede virtual

Você pode excluir uma rede virtual somente se não houver recursos conectados a ela. Se houver recursos conectados a qualquer sub-rede na rede virtual, primeiro exclua os recursos que estão conectados a todas as sub-redes na rede virtual. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos conectados as sub-redes, leia a documentação para cada tipo de recurso que deseja excluir. Para excluir uma rede virtual:

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual que deseja excluir.
3. Confirme que não há nenhum dispositivo conectado à rede virtual selecionando **Dispositivos conectados** em **CONFIGURAÇÕES**. Se houver dispositivos conectados, você deve excluí-los antes de excluir a rede virtual. Se não houver dispositivos conectados, selecione **Visão geral**.
4. Selecione **Excluir**.
5. Para confirmar a exclusão da rede virtual, selecione **Sim**.

**Comandos**

- CLI do Azure: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Permissões

Para executar tarefas em redes virtuais, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Ler uma rede virtual              |
|Microsoft.Network/virtualNetworks/write  |   Criar ou atualizar uma rede virtual  |
|Microsoft.Network/virtualNetworks/delete |   Excluir uma rede virtual            |

## <a name="next-steps"></a>Próximas etapas

- Criar uma rede virtual usando [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para redes virtuais
