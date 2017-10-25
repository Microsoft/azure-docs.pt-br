---
title: Criar, alterar ou excluir um adaptador de rede do Azure | Microsoft Docs
description: "Aprenda o que é um adaptador de rede e como criar, alterar suas configurações e excluir um."
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
ms.openlocfilehash: 7dafb491cec908ffbb3683991919654f3d3eb452
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-network-interface"></a>Criar, alterar ou excluir um adaptador de rede

Saiba como criar, alterar as configurações e excluir um adaptador de rede. Um adaptador de rede permite que uma máquina virtual do Azure comunique-se com a Internet, com o Azure e com recursos locais. Ao criar uma máquina virtual usando o Portal do Azure, o portal criará um adaptador de rede com as configurações padrão para você. Em vez disso, você pode optar por criar adaptadores de rede com configurações personalizadas e adicionar um ou mais adaptadores de rede a uma máquina virtual ao criá-la. Convém também alterar as configurações padrão de um adaptador de rede existente. Este artigo explica como criar um adaptador de rede com configurações personalizadas, alterar as configurações existentes, como a atribuição de filtro de rede (grupos de segurança de rede), atribuição de sub-rede, configurações do servidor DNS e encaminhamento de IP, e excluir um adaptador de rede.

Se você precisar adicionar, alterar ou remover endereços IP de um adaptador de rede, leia o artigo [Gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você precisar adicionar, ou remover, adaptadores de rede a máquinas virtuais, leia o artigo [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md).


## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites para adaptadores de rede.
- Faça logon no [Portal](https://portal.azure.com) do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se usar comandos do PowerShell para concluir as tarefas neste artigo, [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você for usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no botão **>_** do Cloud Shell na parte superior do [Portal](https://portal.azure.com).

## <a name="create-a-network-interface"></a>Criar um adaptador de rede

Ao criar uma máquina virtual usando o Portal do Azure, o portal cria um adaptador de rede com as configurações padrão para você. Se você preferir especificar todas as configurações de seu adaptador de rede, crie um adaptador de rede com configurações personalizadas e anexe-o a uma máquina virtual durante a criação de uma (usando o PowerShell ou a CLI do Azure). Você também pode criar um adaptador de rede e adicioná-lo a uma máquina virtual existente (usando o PowerShell ou a CLI do Azure). Para saber como criar uma máquina virtual com um adaptador de rede existente, ou adicionar e remover adaptadores de rede de máquinas virtuais existentes, leia o artigo [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md). Antes de criar um adaptador de rede, você deve ter uma [rede virtual](virtual-networks-create-vnet-arm-pportal.md) existente no mesmo local e assinatura na qual criou um adaptador de rede.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **adaptadores de rede** que aparece, clique em **+ Adicionar**.
4. Na folha **Criar interface de rede** que é exibida, digite ou selecione valores para as seguintes configurações e clique em **Criar**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado. Ao longo do tempo, você provavelmente terá vários adaptadores de rede em sua assinatura do Azure. Leia o artigo [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obter sugestões ao criar uma convenção de nomenclatura para facilitar o gerenciamento de vários adaptadores de rede. O nome não pode ser alterado após a criação do adaptador de rede.|
    |Rede virtual|Sim|Selecione a rede virtual para o adaptador de rede. Você só pode atribuir um adaptador de rede a uma rede virtual que exista na mesma assinatura e local que o adaptador de rede. Após a criação de um adaptador de rede, não será possível alterar a rede virtual à qual ele foi atribuído. A máquina virtual à qual você adiciona o adaptador de rede também deve existir no mesmo local e assinatura que o adaptador de rede.|
    |Sub-rede|Sim|Selecione uma sub-rede na rede virtual selecionada. Depois de criar o adaptador de rede, você pode alterar a sub-rede na qual ele foi atribuído.|
    |Atribuição de endereço IP privado|Sim| Nessa configuração, você está escolhendo o método de atribuição para o endereço IPv4. Escolha entre os seguintes métodos de atribuição: **Dinâmico:** ao selecionar essa opção, o Azure atribui automaticamente um endereço disponível do espaço de endereços da sub-rede selecionada. O Azure pode atribuir um endereço diferente a um adaptador de rede quando a máquina virtual na qual ele está for iniciada após permanecer em um estado parado (desalocado). O endereço permanece o mesmo se a máquina virtual for reiniciada sem ter permanecido no estado parado (desalocado). **Estático:** ao selecionar essa opção, você deve atribuir manualmente um endereço IP disponível de dentro do espaço de endereço da sub-rede selecionada. Endereços estáticos não mudam até que você os altere ou o adaptador de rede seja excluído. Você pode alterar o método de atribuição após a criação do adaptador de rede. O servidor DHCP do Azure atribui esse endereço ao adaptador de rede dentro do sistema operacional da máquina virtual.|
    |Grupo de segurança de rede|Não| Deixe definido como **Nenhum**, selecione um [grupo de segurança de rede](virtual-networks-nsg.md) existente ou [crie um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md). Os grupos de segurança de rede permitem o controle do fluxo de entrada e saída do tráfego de rede em um adaptador de rede. Você pode aplicar um ou nenhum grupo de segurança de rede a um adaptador de rede. Também é possível aplicar um ou nenhum grupo de segurança de rede à sub-rede à qual o adaptador de rede foi atribuído. Às vezes, quando um grupo de segurança de rede é aplicado a um adaptador de rede e à sub-rede à qual o adaptador de rede foi atribuído, podem ocorrer resultados inesperados. Para solucionar problemas de grupos de segurança de rede aplicados a adaptadores de rede e sub-redes, leia o artigo [Solucionar problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |Assinatura|Sim|Selecione uma das suas [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure. A máquina virtual que você anexa a um adaptador de rede, e a rede virtual à qual você se conecta, devem existir na mesma assinatura.|
    |Endereço IP privado (IPv6)|Não| Se você marcar essa caixa de seleção, um endereço IPv6 será atribuído ao adaptador de rede, além do endereço IPv4 atribuído ao adaptador de rede. Confira a seção [IPv6](#IPv6) deste artigo para obter informações importantes sobre o uso de IPv6 com adaptadores de rede. Não é possível selecionar um método de atribuição para o endereço IPv6. Se você optar por atribuir um endereço IPv6, ele será atribuído com o método dinâmico.
    |O nome do IPv6 (aparece somente quando a caixa de seleção **Endereço IP privado (IPv6)** estiver marcada) |Sim, se a caixa de seleção **Endereço IP privado (IPv6)** estiver marcada.| Esse nome é atribuído a uma configuração de IP secundária para o adaptador de rede. Saiba mais sobre as configurações de IP na seção [Exibir configurações de adaptador de rede](#view-network-interface-settings) deste artigo.|
    |Grupo de recursos|Sim|Selecione um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente ou crie um. Um adaptador de rede pode existir no mesmo grupo, ou em um grupo de recursos diferente, da máquina virtual à qual ele foi anexado ou da rede virtual à qual você o conectou.|
    |Local|Sim|A máquina virtual que você anexa a um adaptador de rede, e a rede virtual à qual você se conecta, devem existir no mesmo [local](https://azure.microsoft.com/regions), também conhecido como região.|

O portal não oferece a opção de atribuir um endereço IP público ao adaptador de rede durante sua criação, apesar de o portal criar um endereço IP público e atribuí-lo a um adaptador de rede quando você cria uma máquina virtual usando o portal. Para saber como adicionar um endereço IP público ao adaptador de rede após criá-lo, leia o artigo [Gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você quiser criar um adaptador de rede com um endereço IP público, use a CLI ou o PowerShell para criar o adaptador de rede.

>[!Note]
> O Azure atribui um endereço MAC ao adaptador de rede somente após a conexão do adaptador de rede a uma máquina virtual, e após a primeira inicialização da máquina virtual. Não é possível especificar o endereço MAC que o Azure atribui ao adaptador de rede. O endereço MAC permanece atribuído ao adaptador de rede até que o mesmo seja excluído ou o endereço IP privado atribuído à configuração de IP primário do adaptador de rede primário seja alterado. Para saber mais sobre endereços IP e configurações de IP, leia o artigo [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Ver as configurações de adaptador de rede

Você pode exibir e alterar a maioria das configurações de um adaptador de rede após sua criação.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique no adaptador de rede que você deseja exibir ou do qual deseja alterar as configurações.
4. As configurações a seguir estão na folha aberta para o adaptador de rede selecionado:
    - **Visão geral:** fornece informações sobre o adaptador de rede, como os endereços IP atribuídos a ele, a rede virtual/sub-rede à qual o adaptador de rede foi atribuído e a máquina virtual à qual o adaptador de rede está conectado (se estiver conectado a uma). A imagem a seguir mostra as configurações de visão geral de um adaptador de rede denominado **mywebserver256**: ![visão geral do adaptador de rede](./media/virtual-network-network-interface/nic-overview.png) Você pode mover um adaptador de rede para um grupo de recursos ou assinatura diferente clicando em (**alterar**) ao lado de **Grupo de recursos** ou **Nome da assinatura**. Se você mover o adaptador de rede, deverá mover todos os recursos relacionados a ele. Se o adaptador de rede estiver conectado a uma máquina virtual, por exemplo, você também deverá mover a máquina virtual e outros recursos relacionados à máquina virtual. Para mover um adaptador de rede, leia o artigo [Mover um recurso para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure.
    - **Configurações de IP:** os endereços IPv4 e IPv6 públicos e privados atribuídos a configurações de IP estão relacionados aqui. Se um endereço IPv6 for atribuído a uma configuração de IP, o endereço não será exibido. Saiba mais sobre as configurações de IP e como adicionar e remover endereços IP no artigo [Configurar endereços IP para um adaptador de rede do Azure](virtual-network-network-interface-addresses.md). Encaminhamento de IP e a atribuição de sub-rede também são configurados nesta seção. Para saber mais sobre essas configurações, confira a seção [Habilitar/desabilitar encaminhamento de IP](#enable-or-disable-ip-forwarding) e [Alterar atribuição de sub-rede](#change-subnet-assignment) deste artigo.
    - **Servidores DNS:** pelos servidores DHCP do Azure, você pode especificar em qual servidor DNS quer atribuir um adaptador de rede. O adaptador de rede pode herdar a configuração da rede virtual à qual foi atribuído, ou ter uma configuração personalizada que substitui a configuração da rede virtual à qual foi atribuído. Para modificar o que é exibido, siga as etapas na seção [Alterar servidores DNS](#change-dns-servers) deste artigo.
    - **NSG (grupo de segurança de rede):** exibe o NSG que está associado ao adaptador de rede (se houver algum). Um NSG contém regras de entrada e saída para filtrar o tráfego de rede para o adaptador de rede. Se houver um NSG associado ao adaptador de rede, o nome do NSG associado será exibido. Para modificar o que é exibido, conclua as etapas no artigo [Gerenciar associações de grupo de segurança de rede](virtual-network-manage-nsg-arm-portal.md#manage-associations).
    - **Propriedades:** exibe as principais configurações do adaptador de rede, incluindo seu endereço MAC (em branco, se o adaptador de rede não estiver anexado a uma máquina virtual) e a assinatura na qual ele existe.
    - **Regras de segurança efetiva:** as regras de segurança serão listadas se o adaptador de rede estiver anexado a uma máquina virtual em execução, e houver um NSG associado ao adaptador de rede, à sub-rede na qual foi atribuído, ou ambos. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md#nsg). Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.
    - **Rotas efetivas:** as rotas são listadas se o adaptador de rede estiver anexado a uma máquina virtual em execução. As rotas são uma combinação das rotas padrão do Azure, quaisquer UDRs (rotas definidas pelo usuário) e quaisquer rotas BGP que possam existir para a sub-rede à qual o adaptador de rede foi atribuído. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de rotas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Para saber mais sobre o padrão do Azure e UDRs, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
    - **Configurações comuns do Azure Resource Manager:** para saber mais sobre as configurações comuns do Azure Resource Manager, leia os artigos [Log de atividades](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Rótulos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

Se um endereço IPv6 for atribuído a um adaptador de rede, a saída do PowerShell retornará o fato de que o endereço foi atribuído, mas não retornará o endereço atribuído. Da mesma forma, a CLI retorna o fato de que o endereço foi atribuído, mas retorna *nulo* na saída para o endereço.

|Ferramenta|Command|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para exibir os adaptadores de rede na assinatura; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para exibir as configurações de um adaptador de rede|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para exibir os adaptadores de rede na assinatura ou para exibir as configurações de um adaptador de rede|

## <a name="change-dns-servers"></a>Alterar os servidores DNS

O servidor DNS é atribuído pelo servidor DHCP do Azure ao adaptador de rede dentro do sistema operacional da máquina virtual. O servidor DNS atribuído é a configuração de servidor DNS de um adaptador de rede. Para saber mais sobre as configurações de resolução de nome de um adaptador de rede, leia o artigo [Resolução de nomes para máquinas virtuais](virtual-networks-name-resolution-for-vms-and-role-instances.md). O adaptador de rede pode herdar as configurações da rede virtual ou usar suas próprias configurações exclusivas que substituem a configuração da rede virtual.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique no adaptador de rede que você deseja exibir ou do qual deseja alterar as configurações.
4. Na folha do adaptador de rede selecionado, clique em **Servidores DNS** em **CONFIGURAÇÕES**.
5. Clique em:
    - **Herdar de rede virtual (padrão)**: escolha essa opção para herdar a configuração do servidor DNS definida para a máquina virtual à qual o adaptador de rede foi atribuído. No nível da rede virtual, é definido um servidor DNS personalizado ou um servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes de host para recursos atribuídos à mesma rede virtual. FQDN deve ser usado para resolver recursos atribuídos a redes virtuais diferentes.
    - **Personalizado**: você pode configurar seu próprio servidor DNS para resolver nomes entre diversas redes virtuais. Digite o endereço IP do servidor que você deseja usar como servidor DNS. O endereço do servidor DNS especificado é atribuído somente a esse adaptador de rede e substitui qualquer configuração de DNS para a rede virtual à qual o adaptador de rede foi atribuído.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Habilitar ou desabilitar o encaminhamento de IP

O encaminhamento de IP permite que a máquina virtual à qual um adaptador de rede está conectado:
- Receba o tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas ao adaptador de rede.
- Envie o tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma das configurações de IP do adaptador de rede.

A configuração deve ser habilitada para cada adaptador de rede conectado à máquina virtual que recebe o tráfego que precisa ser encaminhado. Uma máquina virtual poderá encaminhar tráfego se tiver vários adaptadores de rede ou um único adaptador de rede conectado a ela. Embora o encaminhamento de IP seja uma configuração do Azure, a máquina virtual também deve executar um aplicativo capaz de encaminhar o tráfego, como, por exemplo, firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma máquina virtual executa aplicativos de rede, a máquina virtual é conhecida como uma solução de virtualização de rede. Você pode exibir uma lista de soluções de virtualização de rede prontas para implantar no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). O encaminhamento de IP normalmente é usado com rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique no adaptador de rede no qual você deseja habilitar ou desabilitar o encaminhamento de IP.
4. Na folha do adaptador de rede selecionado, clique em **Configurações de IP** na seção **CONFIGURAÇÕES**.
5. Clique em **habilitado** ou **desabilitado** (configuração padrão) para alterar a configuração.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Alterar atribuição de sub-rede

Você pode alterar a sub-rede, mas não a rede virtual, à qual o adaptador de rede foi atribuído.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique no adaptador de rede que você deseja exibir ou do qual deseja alterar as configurações.
4. Clique em **Configurações de IP** em **CONFIGURAÇÕES** na folha do adaptador de rede que você selecionou. Se algum endereço IP privado para qualquer uma das configurações de IP listadas tiver **(Estático)** ao lado, altere o método de atribuição de endereço IP para dinâmico executando as etapas a seguir. Todos os endereços IP privados devem ser atribuídos com o método de atribuição dinâmica a alterar a atribuição de sub-rede para o adaptador de rede. Se os endereços forem atribuídos com o método dinâmico, siga para a etapa cinco. Se algum endereço IPv4 for atribuído com o método de atribuição estático, conclua as seguintes etapas para alterar o método de atribuição para dinâmico:
    - Clique na configuração de IP da qual você deseja alterar o método de atribuição de endereço IPv4 na lista de configurações de IP.
    - Na folha que aparece para configuração de IP, clique em **Dinâmico** para o método de **atribuição**. Você não pode atribuir um endereço IPv6 com o método de atribuição estática.
    - Clique em **Salvar**.
5. Selecione a sub-rede à qual você deseja conectar o adaptador de rede na lista suspensa **Sub-rede**.
6. Clique em **Salvar**. Novos endereços dinâmicos são atribuídos do intervalo de endereços da sub-rede para a nova sub-rede. Depois de atribuir o adaptador de rede a uma nova sub-rede, você poderá atribuir um endereço IPv4 estático a partir do novo intervalo de endereços de sub-rede, se preferir. Para saber mais sobre como adicionar, alterar e remover endereços IP para um adaptador de rede, leia o artigo [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Excluir um adaptador de rede

Você pode excluir um adaptador de rede desde que ele não esteja conectado a uma máquina virtual. Se ele estiver conectado a uma máquina virtual, primeiro você deve colocar a máquina virtual no estado parado (desalocado) e desconectar o adaptador de rede da máquina virtual, antes de poder exclui-lo. Para desanexar um adaptador de rede de uma máquina virtual, conclua as etapas na seção [Desanexar um adaptador de rede de uma máquina virtual](virtual-network-network-interface-vm.md#vm-remove-nic) do artigo [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md). A exclusão da máquina virtual desconecta todos os adaptadores de rede conectados a ela, mas não exclui os adaptadores de rede.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Clique com o botão direito no adaptador de rede que você quer excluir e clique em **Excluir**.
4. Clique em **Sim** para confirmar a exclusão do adaptador de rede.

Quando você exclui um adaptador de rede, os endereços MAC ou IP atribuídos a ele são liberados.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual com vários adaptadores de rede ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
