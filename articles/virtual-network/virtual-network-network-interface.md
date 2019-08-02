---
title: Criar, alterar ou excluir um adaptador de rede do Azure
titlesuffix: Azure Virtual Network
description: Aprenda o que é um adaptador de rede e como criar, alterar suas configurações e excluir um.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: d928ca458be8e412a91d93efdacab19425f0f2fd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695676"
---
# <a name="create-change-or-delete-a-network-interface"></a>Criar, alterar ou excluir um adaptador de rede

Saiba como criar, alterar as configurações e excluir um adaptador de rede. Um adaptador de rede permite que uma Máquina Virtual do Azure se comunique com a internet, o Azure e com recursos locais. Ao criar uma máquina virtual usando o Portal do Azure, o portal criará um adaptador de rede com as configurações padrão para você. Em vez disso, você pode optar por criar adaptadores de rede com configurações personalizadas e adicionar um ou mais adaptadores de rede a uma máquina virtual ao criá-la. Convém também alterar as configurações padrão de um adaptador de rede existente. Este artigo explica como criar um adaptador de rede com configurações personalizadas, alterar as configurações existentes, como a atribuição de filtro de rede (grupos de segurança de rede), atribuição de sub-rede, configurações do servidor DNS e encaminhamento de IP, e excluir um adaptador de rede.

Se você precisar adicionar, alterar ou remover endereços IP de um adaptador de rede, consulte [Gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você precisar adicionar, ou remover, adaptadores de rede de máquinas virtuais, veja [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta em que você realizou o logon, ou se conectou ao Azure, deve estar atribuída à função do [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída a ações adequadas listadas em [Permissões](#permissions).

## <a name="create-a-network-interface"></a>Criar um adaptador de rede

Ao criar uma máquina virtual usando o Portal do Azure, o portal cria um adaptador de rede com as configurações padrão para você. Se você preferir especificar todas as configurações de seu adaptador de rede, crie um adaptador de rede com configurações personalizadas e anexe-o a uma máquina virtual durante a criação de uma (usando o PowerShell ou a CLI do Azure). Você também pode criar um adaptador de rede e adicioná-lo a uma máquina virtual existente (usando o PowerShell ou a CLI do Azure). Para saber como criar uma máquina virtual com um adaptador de rede existente ou como adicionar um, ou remover adaptadores de rede de máquinas virtuais existente, veja [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md). Antes de criar um adaptador de rede, você deve ter uma [rede virtual](manage-virtual-network.md) existente no mesmo local e assinatura na qual criou um adaptador de rede.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione **+ Adicionar** em **interfaces de Rede**.
3. Insira ou selecione valores para as seguintes configurações e selecione **Criar**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado. Ao longo do tempo, você provavelmente terá vários adaptadores de rede em sua assinatura do Azure. Para sugestões ao criar uma convenção de nomenclatura para facilitar o gerenciamento de vários adaptadores de rede, veja [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). O nome não pode ser alterado após a criação do adaptador de rede.|
    |Rede virtual|Sim|Selecione a rede virtual para o adaptador de rede. Você só pode atribuir um adaptador de rede a uma rede virtual que exista na mesma assinatura e local que o adaptador de rede. Após a criação de um adaptador de rede, não será possível alterar a rede virtual à qual ele foi atribuído. A máquina virtual à qual você adiciona o adaptador de rede também deve existir no mesmo local e assinatura que o adaptador de rede.|
    |Subnet|Sim|Selecione uma sub-rede na rede virtual selecionada. Depois de criar o adaptador de rede, você pode alterar a sub-rede na qual ele foi atribuído.|
    |Atribuição de endereço IP privado|Sim| Nessa configuração, você está escolhendo o método de atribuição para o endereço IPv4. Escolha entre os seguintes métodos de atribuição: **Dinâmico:** Ao selecionar essa opção, o Azure atribui automaticamente o próximo endereço disponível do espaço de endereços da sub-rede selecionada. **Estático:** Ao selecionar essa opção, você deve atribuir manualmente um endereço IP disponível de dentro do espaço de endereço da sub-rede selecionada. Endereços estáticos e dinâmicos não mudam até que você os altere ou o adaptador de rede seja excluído. Você pode alterar o método de atribuição após a criação do adaptador de rede. O servidor DHCP do Azure atribui esse endereço ao adaptador de rede dentro do sistema operacional da máquina virtual.|
    |Grupo de segurança de rede|Não| Deixe definido como **Nenhum**, selecione um [grupo de segurança de rede](security-overview.md) existente ou [crie um grupo de segurança de rede](tutorial-filter-network-traffic.md). Os grupos de segurança de rede permitem o controle do fluxo de entrada e saída do tráfego de rede em um adaptador de rede. Você pode aplicar um ou nenhum grupo de segurança de rede a um adaptador de rede. Também é possível aplicar um ou nenhum grupo de segurança de rede à sub-rede à qual o adaptador de rede foi atribuído. Às vezes, quando um grupo de segurança de rede é aplicado a um adaptador de rede e à sub-rede à qual o adaptador de rede foi atribuído, podem ocorrer resultados inesperados. Para solucionar problemas de grupos de segurança de rede aplicados a adaptadores de rede e sub-redes, leia o artigo [Solucionar problemas de grupos de segurança de rede](diagnose-network-traffic-filter-problem.md).|
    |Assinatura|Sim|Selecione uma das suas [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure. A máquina virtual que você anexa a um adaptador de rede, e a rede virtual à qual você se conecta, devem existir na mesma assinatura.|
    |Endereço IP privado (IPv6)|Não| Se você marcar essa caixa de seleção, um endereço IPv6 será atribuído ao adaptador de rede, além do endereço IPv4 atribuído ao adaptador de rede. Confira a seção IPv6 deste artigo para obter informações importantes sobre o uso de IPv6 com adaptadores de rede. Não é possível selecionar um método de atribuição para o endereço IPv6. Se você optar por atribuir um endereço IPv6, ele será atribuído com o método dinâmico.
    |O nome do IPv6 (aparece somente quando a caixa de seleção **Endereço IP privado (IPv6)** estiver marcada) |Sim, se a caixa de seleção **Endereço IP privado (IPv6)** estiver marcada.| Esse nome é atribuído a uma configuração de IP secundária para o adaptador de rede. Saiba mais sobre as configurações de IP em [Exibir configurações de adaptador de rede](#view-network-interface-settings).|
    |Grupo de recursos|Sim|Selecione um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente ou crie um. Um adaptador de rede pode existir no mesmo grupo, ou em um grupo de recursos diferente, da máquina virtual à qual ele foi anexado ou da rede virtual à qual você o conectou.|
    |Location|Sim|A máquina virtual que você anexa a um adaptador de rede, e a rede virtual à qual você se conecta, devem existir no mesmo [local](https://azure.microsoft.com/regions), também conhecido como região.|

O portal não oferece a opção de atribuir um endereço IP público ao adaptador de rede durante sua criação, apesar de o portal criar um endereço IP público e atribuí-lo a um adaptador de rede quando você cria uma máquina virtual usando o portal. Para saber como adicionar um endereço IP público ao adaptador de rede após criá-lo, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você quiser criar um adaptador de rede com um endereço IP público, use a CLI ou o PowerShell para criar o adaptador de rede.

O portal não fornece a opção de atribuir o adaptador de rede a grupos de segurança de aplicativo durante sua criação, mas a CLI do Azure e o PowerShell oferecem essa opção. Você só pode atribuir um adaptador de rede existente a um grupo de segurança de aplicativo usando o portal quando o adaptador de rede está conectado a uma máquina virtual. Para saber como atribuir um adaptador de rede a um grupo de segurança de aplicativo, confira [Adicionar a ou remover de grupos de segurança de aplicativo](#add-to-or-remove-from-application-security-groups).

>[!Note]
> O Azure atribui um endereço MAC ao adaptador de rede somente após a conexão do adaptador de rede a uma máquina virtual, e após a primeira inicialização da máquina virtual. Não é possível especificar o endereço MAC que o Azure atribui ao adaptador de rede. O endereço MAC permanece atribuído ao adaptador de rede até que o mesmo seja excluído ou o endereço IP privado atribuído à configuração de IP primário do adaptador de rede primário seja alterado. Para saber mais sobre endereços IP e configurações de IP, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md)

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Ver as configurações de adaptador de rede

Você pode exibir e alterar a maioria das configurações de um adaptador de rede após sua criação. O portal não mostra o sufixo DNS ou a associação do grupo de segurança de aplicativo para o adaptador de rede. Você pode usar [comandos](#view-settings-commands) do PowerShell ou da CLI do Azure para exibir o sufixo DNS e a associação do grupo de segurança de aplicativo.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione o adaptador de rede que você deseja exibir ou alterar as configurações da lista.
3. Os itens a seguir são listados para o adaptador de rede selecionado:
   - **Visão geral:** Fornece informações sobre o adaptador de rede, como os endereços IP atribuídos a ele, a rede virtual/sub-rede à qual o adaptador de rede foi atribuído e a máquina virtual à qual o adaptador de rede está conectado (se estiver conectado a uma). A figura a seguir mostra as configurações de visão geral para um adaptador de rede denominado **mywebserver256**: ![Visão geral da interface de rede](./media/virtual-network-network-interface/nic-overview.png)

     Você pode mover um adaptador de rede para um outro grupo de recursos ou assinatura selecionando (**alterar**) ao lado de **grupo de recursos** ou **nome da assinatura**. Se você mover o adaptador de rede, deverá mover todos os recursos relacionados a ele. Se o adaptador de rede estiver conectado a uma máquina virtual, por exemplo, você também deverá mover a máquina virtual e outros recursos relacionados à máquina virtual. Para mover um adaptador de rede, veja [Mover um recurso para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure.
   - **Configurações de IP:** Os endereços IPv4 e IPv6 públicos e privados atribuídos a configurações de IP estão relacionados aqui. Se um endereço IPv6 for atribuído a uma configuração de IP, o endereço não será exibido. Para saber mais sobre as configurações de IP e como adicionar e remover endereços IP, veja [Configurar endereços IP para um adaptador de rede do Azure](virtual-network-network-interface-addresses.md). Encaminhamento de IP e a atribuição de sub-rede também são configurados nesta seção. Para saber mais sobre essas configurações, veja [Habilitar/desabilitar encaminhamento de IP](#enable-or-disable-ip-forwarding) e [Alterar atribuição de sub-rede](#change-subnet-assignment).
   - **Servidores DNS:** Pelos servidores DHCP do Azure, você pode especificar em qual servidor DNS quer atribuir um adaptador de rede. O adaptador de rede pode herdar a configuração da rede virtual à qual foi atribuído, ou ter uma configuração personalizada que substitui a configuração da rede virtual à qual foi atribuído. Para modificar o que é exibido, consulte [servidores DNS de alteração](#change-dns-servers).
   - **NSG (Grupo de Segurança de Rede):** Exibe o NSG que está associado ao adaptador de rede (se houver algum). Um NSG contém regras de entrada e saída para filtrar o tráfego de rede para o adaptador de rede. Se houver um NSG associado ao adaptador de rede, o nome do NSG associado será exibido. Para modificar o que é exibido, consulte [Associar ou desassociar um Grupo de Segurança de Rede](#associate-or-dissociate-a-network-security-group).
   - **Propriedades:** Exibe as principais configurações do adaptador de rede, incluindo seu endereço MAC (em branco, se o adaptador de rede não estiver anexado a uma máquina virtual) e a assinatura na qual ele existe.
   - **Regras de segurança em vigor:**  As regras de segurança serão listadas se o adaptador de rede estiver anexado a uma máquina virtual em execução, e houver um NSG associado ao adaptador de rede, à sub-rede na qual foi atribuído, ou ambos. Para saber mais sobre o que é exibido, consulte [Exibir regras de segurança em vigor](#view-effective-security-rules). Para saber mais sobre NSGs, confira [Grupos de segurança de rede](security-overview.md).
   - **Rotas efetivas:** As rotas são listadas se o adaptador de rede estiver anexado a uma máquina virtual em execução. As rotas são uma combinação de todas as rotas padrão do Azure, qualquer rota definida pelo usuário e rotas BGP que podem existir para a sub-rede à qual o adaptador de rede foi atribuído. Para saber mais sobre o que é exibido, consulte [Exibir rotas em vigor](#view-effective-routes). Para saber mais sobre as rotas padrão do Azure e as rotas definidas pelo usuário, consulte [Visão geral de rotas](virtual-networks-udr-overview.md).
   - **Configurações comuns do Azure Resource Manager:**  Para saber mais sobre as configurações comuns do Azure Resource Manager, veja [Log de atividades](../azure-monitor/platform/activity-logs-overview.md), [Controle de acesso (IAM)](../role-based-access-control/overview.md), [Rótulos](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script de automação](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Comandos**

Se um endereço IPv6 for atribuído a um adaptador de rede, a saída do PowerShell retornará o fato de que o endereço foi atribuído, mas não retornará o endereço atribuído. Da mesma forma, a CLI retorna o fato de que o endereço foi atribuído, mas retorna *nulo* na saída para o endereço.

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic) para exibir os adaptadores de rede na assinatura; [az network nic show](/cli/azure/network/nic) para exibir as configurações de um adaptador de rede|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) para exibir os adaptadores de rede nas configurações de assinatura ou o modo de exibição para um adaptador de rede|

## <a name="change-dns-servers"></a>Alterar os servidores DNS

O servidor DNS é atribuído pelo servidor DHCP do Azure ao adaptador de rede dentro do sistema operacional da máquina virtual. O servidor DNS atribuído é a configuração de servidor DNS de um adaptador de rede. Para saber mais sobre as configurações de resolução de nome de um adaptador de rede, leia o artigo [Resolução de nomes para máquinas virtuais](virtual-networks-name-resolution-for-vms-and-role-instances.md). O adaptador de rede pode herdar as configurações da rede virtual ou usar suas próprias configurações exclusivas que substituem a configuração da rede virtual.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione o adaptador de rede que você deseja alterar o servidor DNS da lista.
3. Selecione **Servidores DNS** em **CONFIGURAÇÕES**.
4. Selecione:
   - **Herdar de rede virtual**: Escolha essa opção para herdar a configuração do servidor DNS definida para a máquina virtual à qual o adaptador de rede foi atribuído. No nível da rede virtual, é definido um servidor DNS personalizado ou um servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes de host para recursos atribuídos à mesma rede virtual. FQDN deve ser usado para resolver recursos atribuídos a redes virtuais diferentes.
   - **Personalizado**: Você pode configurar seu próprio servidor DNS para resolver nomes entre diversas redes virtuais. Digite o endereço IP do servidor que você deseja usar como servidor DNS. O endereço do servidor DNS especificado é atribuído somente a esse adaptador de rede e substitui qualquer configuração de DNS para a rede virtual à qual o adaptador de rede foi atribuído.
     >[!Note]
     >Se a VM usa uma NIC que faz parte de um conjunto de disponibilidade, todos os servidores DNS especificados para cada uma das VMs de todas as NICs que fazem parte do conjunto de disponibilidade serão herdados.
5. Clique em **Salvar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Habilitar ou desabilitar o encaminhamento de IP

O encaminhamento de IP permite que a máquina virtual à qual um adaptador de rede está conectado:
- Receba o tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas ao adaptador de rede.
- Envie o tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma das configurações de IP do adaptador de rede.

A configuração deve ser habilitada para cada adaptador de rede conectado à máquina virtual que recebe o tráfego que precisa ser encaminhado. Uma máquina virtual poderá encaminhar tráfego se tiver vários adaptadores de rede ou um único adaptador de rede conectado a ela. Embora o encaminhamento de IP seja uma configuração do Azure, a máquina virtual também deve executar um aplicativo capaz de encaminhar o tráfego, como, por exemplo, firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma máquina virtual executa aplicativos de rede, a máquina virtual é conhecida como uma solução de virtualização de rede. Você pode exibir uma lista de soluções de virtualização de rede prontas para implantar no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). O encaminhamento de IP normalmente é usado com rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, consulte [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione o adaptador de rede que você deseja habilitar ou desabilitar o encaminhamento de IP.
3. Selecione **configurações de IP** na seção **CONFIGURAÇÕES**.
4. Selecione **Habilitado** ou **Desabilitado** (configuração padrão) para alterar a configuração.
5. Clique em **Salvar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alterar atribuição de sub-rede

Você pode alterar a sub-rede, mas não a rede virtual, à qual o adaptador de rede foi atribuído.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione o adaptador de rede que você deseja alterar a atribuição de sub-rede.
3. Selecione **Configurações de IP** em **CONFIGURAÇÕES**. Se algum endereço IP privado para qualquer uma das configurações de IP listadas tiver **(Estático)** ao lado, altere o método de atribuição de endereço IP para dinâmico executando as etapas a seguir. Todos os endereços IP privados devem ser atribuídos com o método de atribuição dinâmica a alterar a atribuição de sub-rede para o adaptador de rede. Se os endereços forem atribuídos com o método dinâmico, siga para a etapa cinco. Se algum endereço IPv4 for atribuído com o método de atribuição estático, conclua as seguintes etapas para alterar o método de atribuição para dinâmico:
   - Selecione a configuração de IP que você deseja alterar o método de atribuição de endereço IPv4 na lista de configurações de IP.
   - Selecione **Dinâmico** para o método de **Atribuição** de endereço de IP privado. Você não pode atribuir um endereço IPv6 com o método de atribuição estática.
   - Clique em **Salvar**.
4. Selecione a sub-rede que você deseja mover o adaptador de rede na lista suspensa **Sub-rede**.
5. Clique em **Salvar**. Novos endereços dinâmicos são atribuídos do intervalo de endereços da sub-rede para a nova sub-rede. Depois de atribuir o adaptador de rede a uma nova sub-rede, você poderá atribuir um endereço IPv4 estático a partir do novo intervalo de endereços de sub-rede, se preferir. Para saber mais sobre como adicionar, alterar e remover endereços IP para um adaptador de rede, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Adicionar ou remover de grupos de segurança de aplicativo

Você só pode adicionar ou remover um adaptador de rede de um grupo de segurança de aplicativo usando o portal quando o adaptador de rede está conectado a uma máquina virtual. Você só pode usar o PowerShell ou a CLI do Azure para adicionar ou remover um adaptador de rede de um grupo de segurança de aplicativo usando o portal quando o adaptador de rede está conectado a uma máquina virtual. Saiba mais sobre [Grupos de segurança de aplicativo](security-overview.md#application-security-groups) e como [criar um grupo de segurança de aplicativo](manage-network-security-group.md).

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece a digitar o nome de uma máquina virtual que tem o adaptador de rede que você deseja adicionar a ou remover de um grupo de segurança de aplicativo. Quando o nome da VM for exibido nos resultados da pesquisa, selecione-o.
2. Em **CONFIGURAÇÕES**, selecione **Rede**.  Selecione **Configurar os grupos de segurança de aplicativo**, selecione os grupos de segurança de aplicativo aos quais deseja adicionar o adaptador de rede, ou desmarque os grupos de segurança de aplicativo dos quais deseja remover o adaptador de rede e, em seguida, selecione **Salvar**. Somente os adaptadores de rede que existem na mesma rede virtual podem ser adicionados ao mesmo grupo de segurança de aplicativo. O grupo de segurança do aplicativo precisa existir no mesmo local que o adaptador de rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associar ou desassociar um Grupo de Segurança de Rede

1. Na caixa de pesquisa na parte superior do portal, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione o adaptador de rede na lista ao qual você deseja associar ou desassociar um Grupo de Segurança de Rede.
3. Selecione **Grupo de Segurança de Rede** em **CONFIGURAÇÕES**.
4. Selecione **Editar**.
5. Selecione **Grupo de Segurança de Rede** e, em seguida, escolha o Grupo de Segurança de Rede que você deseja associar ao adaptador de rede, ou então selecione **Nenhum** para desassociar um Grupo de Segurança de Rede.
6. Clique em **Salvar**.

**Comandos**

- CLI do Azure: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Excluir um adaptador de rede

Você pode excluir um adaptador de rede desde que ele não esteja conectado a uma máquina virtual. Se um adaptador de rede estiver anexado a uma máquina virtual, será necessário primeiro colocar a máquina virtual no estado parado (desalocado) para então desanexar o adaptador de rede dela. Para desanexar um adaptador de rede de uma máquina virtual, conclua as etapas na seção [Desanexar um adaptador de rede de uma máquina virtual](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Não será possível desanexar um adaptador de rede de uma máquina virtual se este for o único anexado a ela. Uma máquina virtual no Azure sempre precisa ter pelo menos um adaptador de rede anexado. A exclusão da máquina virtual desconecta todos os adaptadores de rede conectados a ela, mas não exclui os adaptadores de rede.

1. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
2. Selecione **...** no lado direito do adaptador de rede que deseja excluir da lista de adaptadores de rede.
3. Selecione **Excluir**.
4. Selecione **Sim** para confirmar a exclusão do adaptador de rede.

Quando você exclui um adaptador de rede, os endereços MAC ou IP atribuídos a ele são liberados.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

Se não for possível se comunicar com uma máquina virtual, as rotas ou regras de segurança do Grupo de Segurança de Rede em vigor para um adaptador de rede poderão estar causando o problema. Você tem as seguintes opções para ajudar a resolver o problema:

### <a name="view-effective-security-rules"></a>Exibir regras de segurança em vigor

As regras de segurança em vigor para cada adaptador de rede anexado a uma máquina virtual são uma combinação das regras que você criou em um Grupo de Segurança de Rede e das [regras de segurança padrão](security-overview.md#default-security-rules). Compreender as regras de segurança em vigor para um adaptador de rede pode ajudar você a determinar porque não é possível comunicar-se com uma máquina virtual. Você pode exibir as regras em vigor para qualquer adaptador de rede anexado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, digite o nome de uma máquina virtual cujas regras de segurança em vigor você deseja exibir. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando **Máquinas virtuais** aparecer nos resultados da pesquisa, selecione essa opção e uma máquina virtual da lista.
2. Selecione **Rede** em **CONFIGURAÇÕES**.
3. Selecione o nome de um adaptador de rede.
4. Selecione **Regras de segurança em vigor** em **SUPORTE + SOLUÇÃO DE PROBLEMAS**.
5. Examine a lista de regras de segurança em vigor para determinar se as regras corretas existem para a comunicação de entrada e saída necessária. Saiba mais sobre o que é mostrado na lista em [Bisão geral de Grupo de Segurança de Rede](security-overview.md).

O recurso de verificação de fluxo IP do Observador de Rede do Azure também pode ajudar a determinar se as regras de segurança estão impedindo a comunicação entre uma máquina virtual e um ponto de extremidade. Para saber mais, consulte [Verificar o fluxo IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas em vigor para os adaptadores de rede anexados a uma máquina virtual são uma combinação de rotas padrão, rotas criadas por você e quaisquer rotas propagadas de redes locais via BGP por meio de um gateway de Rede Virtual do Azure. Compreender as rotas de segurança em vigor para um adaptador de rede pode ajudar você a determinar porque não é possível comunicar-se com uma máquina virtual. Você pode exibir as rotas em vigor para qualquer adaptador de rede anexado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, digite o nome de uma máquina virtual cujas regras de segurança em vigor você deseja exibir. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando **Máquinas virtuais** aparecer nos resultados da pesquisa, selecione essa opção e uma máquina virtual da lista.
2. Selecione **Rede** em **CONFIGURAÇÕES**.
3. Selecione o nome de um adaptador de rede.
4. Selecione **Rotas em vigor** em **SUPORTE + SOLUÇÃO DE PROBLEMAS**.
5. Examine a lista de rotas em vigor para determinar se as rotas corretas existem para a comunicação de entrada e saída necessária. Saiba mais sobre o que é mostrado na lista em [Visão geral de roteamento](virtual-networks-udr-overview.md).

O recurso de próximo salto do Observador de Rede do Azure também pode ajudar a determinar se as rotas estão impedindo a comunicação entre uma máquina virtual e um ponto de extremidade. Para saber mais, consulte [Próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Permissões

Para executar tarefas em interfaces de rede, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as permissões apropriadas listadas na tabela a seguir:

| Action                                                                     | NOME                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Obter interface de rede                                     |
| Microsoft.Network/networkInterfaces/write                                  | Criar ou atualizar uma placa de interface de rede                        |
| Microsoft.Network/networkInterfaces/join/action                            | Anexar uma interface de rede a uma máquina virtual.           |
| Microsoft.Network/networkInterfaces/delete                                 | Excluir interface de rede                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/acti                | Ingressa um recurso em uma interface de rede através de um serviço...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Obter tabela de rotas em vigor para um adaptador de rede               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Obter grupos de segurança efetivos da interface de rede           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Obter balanceadores de carga de interface de rede                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Obter uma associação de serviço                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Criar ou atualizar uma associação de serviço                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Excluir uma associação de serviço                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Validar associação de serviço                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Obter uma configuração IP da interface de rede                    |

## <a name="next-steps"></a>Próximas etapas

- Criar uma VM com várias NICs usando a [CLI do Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma VM NIC com vários endereços IPv4 usando a [CLI do Azure](virtual-network-multiple-ip-addresses-cli.md) ou [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Criar uma VM de NIC único com um endereço IPv6 privado (atrás de um Azure Load Balancer) usando a [CLI do Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), o [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou o [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma interface de rede usando o [PowerShell](powershell-samples.md), os scripts de exemplo da [CLI do Azure](cli-samples.md) ou os modelos do [Azure Resource Manager](template-samples.md)
- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais
