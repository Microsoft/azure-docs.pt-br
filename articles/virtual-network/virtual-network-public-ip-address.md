---
title: "Criar, alterar ou excluir um endereço IP público do Azure | Microsoft Docs"
description: "Aprenda a criar, alterar ou excluir um endereço IP público."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 5bffea350061231e1dc664b3abcbf79950a54402
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou excluir um endereço IP público

Saiba mais sobre um endereço IP público e como criá-lo, alterá-lo e excluí-lo. Um endereço IP público é um recurso com suas próprias definições configuráveis. A atribuição de um endereço IP público a outros recursos do Azure permite:
- Conectividade de entrada com a Internet para recursos como Máquinas Virtuais do Azure, Conjuntos de Dimensionamento de Máquinas Virtuais do Azure, Gateway de VPN do Azure, gateways de aplicativos e Azure Load Balancers voltados para a Internet. Os recursos do Azure não podem receber comunicação de entrada da Internet sem um endereço IP público atribuído. Embora alguns recursos do Azure sejam inerentemente acessíveis por meio de endereços IP públicos, outros recursos devem ter endereços IP públicos atribuídos a eles para serem acessíveis pela Internet.
- Conectividade de saída à Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode se comunicar pela saída com a Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede convertido pelo Azure em um endereço público imprevisível. Atribuir um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para saber mais, confira [Criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre conexões de saída usando recursos do Azure, leia o artigo [Entendendo as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites para endereços IP públicos.
- Faça logon no [Portal](https://portal.azure.com) do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se usar comandos do PowerShell para concluir as tarefas neste artigo, [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você for usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no botão **>_** do Cloud Shell na parte superior do [Portal](https://portal.azure.com).

Os endereços IP públicos têm um encargo nominal. Para ver os preços, acesse a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
3. Clique em **+ Adicionar** na folha **Endereço IP Público** que é exibida.
4. Insira ou selecione valores para as seguintes configurações na folha **Criar endereço IP público** que é exibida e clique em **Criar**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU **Básico**.  Você não pode alterar a SKU depois que o endereço IP público é criado. Uma máquina virtual autônoma, máquinas virtuais em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais podem usar SKUs Básicos ou Standard.  Não é permitido misturar SKUs entre máquinas virtuais em conjuntos de disponibilidade e aquelas em conjuntos de dimensionamento. SKU **Básico**: se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Nenhuma* por padrão. Você pode optar por selecionar uma zona de disponibilidade para assegurar uma zona específica para seu endereço IP público. SKU **Standard**: um IP público de SKU Standard pode ser associado a uma máquina virtual ou a um front-end de balanceador de carga. Se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Com redundância de zona* por padrão. Para obter informações sobre zonas de disponibilidade, consulte configuração **Zona de disponibilidade**. O SKU padrão será necessário se você associar o endereço a um Load Balancer Standard. Para saber mais sobre os Load Balancers Standard, consulte [SKU Standard do Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O SKU Standard está em versão prévia. Antes de criar um endereço IP público do SKU Standard, você deve primeiro concluir as etapas em [Registrar-se para a versão prévia do SKU Standard](#register-for-the-standard-sku-preview) e criar o endereço IP público em uma localização (região) com suporte. Para obter uma lista de localizações com suporte, consulte [Disponibilidade da região](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) e monitore a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) para obter suporte de região adicional. Quando você atribui um endereço IP público de SKU Standard ao adaptador de rede de uma máquina virtual, você deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.|
    |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado.|
    |Versão IP|Sim| Selecione IPv4 ou IPv6. Embora endereços IPv4 públicos possam ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga voltado para a Internet. O balanceador de carga pode balancear o tráfego IPv6 para máquinas virtuais do Azure. Saiba mais sobre [balanceamento de carga de tráfego IPv6 para máquinas virtuais](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você tiver selecionado o **SKU Standard**, você não terá a opção de selecionar *IPv6*. Você só pode criar um endereço IPv4 ao usar o **SKU Standard**.|
    |Atribuição de endereço IP|Sim|**Dinâmico:** endereços dinâmicos são atribuídos somente depois que o endereço IP público é associado a um adaptador de rede conectado a uma máquina virtual e essa máquina virtual é iniciada pela primeira vez. Os endereços dinâmicos poderão ser alterados se a máquina virtual à qual o adaptador de rede está conectado for interrompida (desalocada). O endereço permanecerá o mesmo se a máquina virtual for reinicializada ou interrompida (mas não desalocada). **Estático:** endereços estáticos são atribuídos quando o endereço IP público é criado. Os endereços estáticos não serão alterados mesmo se a máquina virtual for colocada no estado interrompido (desalocado). O endereço só é liberado quando o adaptador de rede é excluído. Você pode alterar o método de atribuição após a criação do adaptador de rede. Se você selecionar *IPv6* para a **Versão do IP**, o método de atribuição será *Dinâmico*. Se você selecionar *Standard* para **SKU**, o método de atribuição será *Estático*.|
    |Tempo limite de ociosidade (minutos)|Não|Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive. Se você selecionar IPv6 para a **versão IP**, esse valor não poderá ser alterado. |
    |Rótulo do nome DNS|Não|Deve ser exclusivo mo local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O serviço DNS público do Azure registra automaticamente o nome e o endereço IP para que você possa se conectar a um recurso com o nome. O Azure acrescenta *location.cloudapp.azure.com* (em que location é a localização que você seleciona) ao nome que você fornece para criar o nome DNS totalmente qualificado. Se você optar por criar duas versões de endereço, o mesmo nome DNS será atribuído aos endereços IPv4 e IPv6. O serviço DNS do Azure contém registros de nome IPv4 e IPv6 AAAA e responde com ambos os registros quando o nome DNS é pesquisado. O cliente escolhe com qual endereço (IPv4 ou IPv6) quer se comunicar.|
    |Criar um endereço IPv6 (ou IPv4)|Não| O que é exibido, IPv4 ou IPv6, depende do que você seleciona para a **versão IP**. Por exemplo, se você selecionar **IPv4** para a **versão IP**, **IPv6** será exibido aqui. Se você selecionar *Standard* para **SKU**, você não terá a opção de criar um endereço IPv6.
    |Nome (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)**)|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|O nome deve ser diferente do nome que você insere para o primeiro **Nome** nessa lista. Se você optar por criar endereços IPv4 e IPv6, o portal criará dois recursos de endereço IP públicos separados, cada um com uma versão do endereço IP atribuída a ele.|
    |Atribuição de endereço IP (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)**)|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|Se a caixa de seleção diz **Criar um endereço IPv4**, você pode selecionar um método de atribuição. Se a caixa de seleção diz **Criar um endereço IPv6**, não é possível selecionar um método de atribuição, pois ele deve ser **Dinâmico**.|
    |Assinatura|Sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você deseja associar o endereço IP público.|
    |Grupo de recursos|Sim|Pode existir no mesmo ou em outro [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que o recurso ao qual você deseja associar o endereço IP público.|
    |Local|Sim|Deve existir na mesma [localização](https://azure.microsoft.com/regions), também referenciada como região, que o recurso ao qual você deseja associar o endereço IP público.|
    |Zona de disponibilidade| Não | Essa configuração só aparecerá se você selecionar uma localização com suporte. Para obter uma lista de localizações com suporte, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). As zonas de disponibilidade estão atualmente em versão prévia. Antes de selecionar uma opção de zona ou com redundância de zona, você deve concluir as etapas em [Registrar-se para a versão prévia de zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#get-started-with-the-availability-zones-preview). Se você tiver selecionado o SKU **Básico**, *Nenhum* será selecionado automaticamente para você. Se você preferir garantir uma zona específica, você poderá selecionar uma. Qualquer uma dessas opções será sem redundância de zona. Se você tiver selecionado o SKU **Standard**, a opção Com redundância de zona será selecionada automaticamente para você e tornará o seu caminho de dados resiliente a falhas de zona. Se você preferir garantir uma zona específica (que não é resiliente a falhas de zona), você poderá selecionar uma.
  

**Comandos**

Embora o portal forneça a opção de criar dois recursos de endereço IP públicos (um IPv4 e um IPv6), os comandos da CLI e do PowerShell a seguir criam um recurso com um endereço para uma versão IP ou o outro. Se você quiser dois recursos de endereço IP público, uma para cada versão IP, deverá executar o comando duas vezes, especificando nomes e versões diferentes para os recursos de endereço IP público. 

|Ferramenta|Command|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Exibir, alterar as configurações ou excluir um endereço IP público

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
3. Na folha **Endereços IP públicos** exibida, clique no nome do endereço IP público que deseja exibir, alterar as configurações ou excluir.
4. Na folha exibida para o endereço IP público, escolha uma das opções a seguir, dependendo da ação que deseja: exibir, excluir ou alterar o endereço IP público.
    - **Exibir**: a seção **Visão Geral** da folha mostra as configurações de chave do endereço IP público, como o adaptador de rede ao qual ele está associado (caso o endereço esteja associado a um adaptador de rede). O portal não mostra a versão do endereço (IPv4 ou IPv6). Para exibir as informações de versão, use o comando do PowerShell ou da CLI para exibir o endereço IP público. Se a versão do endereço IP for IPv6, o endereço atribuído não será exibido pelo portal, pelo PowerShell nem pela CLI. 
    - **Excluir**: para excluir o endereço IP público, clique em **Excluir** na seção **Visão Geral** da folha. Se o endereço estiver associado a uma configuração de IP, ele não poderá ser excluído. Se, no momento, o endereço estiver associado a uma configuração, clique em **Desassociar** para desassociar o endereço da configuração de IP.
    - **Alterar**: clique em **Configuração**. Altere as configurações usando as informações na etapa 4 da seção [Criar um endereço IP público](#create-a-public-ip-address) deste artigo. Para alterar a atribuição para um endereço IPv4 de estático para dinâmico, primeiramente você deve desassociar o endereço IPv4 público da configuração de IP à qual ele está associado. Você pode alterar o método de atribuição para dinâmico e clicar em **Associar** para associar o endereço IP à mesma configuração de IP, a uma configuração diferente ou deixá-lo desassociado. Para desassociar um endereço IP público, na seção **Visão Geral**, clique em **Desassociar**.

>[!WARNING]
>Ao alterar o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (caso tenha definido um), um endereço IP dinâmico pode ser alterado quando a máquina virtual é iniciada depois de ter ficado no estado interrompida (desalocada). Para impedir que o endereço seja alterado, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para listar endereços IP públicos; [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para mostrar as configurações; [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para atualizar e [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) para excluir|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para recuperar um objeto de endereço IP público e exibir suas configurações; [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para atualizar as configurações e [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) para excluir|

## <a name="register-for-the-standard-sku-preview"></a>Registrar-se para a versão prévia do SKU Standard

> [!NOTE]
> Os recursos na versão prévia podem não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Não há suporte para os recursos na versão prévia, eles podem ter funcionalidades restritas e podem não estar disponíveis em todas as localizações do Azure. 

Antes que você possa criar um endereço IP público do SKU Standard, você deve primeiro registrar-se para a versão prévia. Para se registrar para a versão prévia, conclua as seguintes etapas:

1. Instale e configure o Azure [PowerShell](/powershell/azure/install-azurerm-ps).
2. Execute o comando `Get-Module -ListAvailable AzureRM` para ver qual versão do módulo AzureRM você instalou. Você deve ter a versão 4.4.0 ou superior instalada. Se você não tiver, você poderá instalar a versão mais recente da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. Faça logon no Azure com o comando `login-azurermaccount`.
4. Insira o comando a seguir para se registrar para a versão prévia:
   
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

5. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

## <a name="next-steps"></a>Próximas etapas
Atribua endereços IP públicos ao criar os seguintes recursos do Azure:

- Máquinas virtuais [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de Dimensionamento de Máquinas Virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
