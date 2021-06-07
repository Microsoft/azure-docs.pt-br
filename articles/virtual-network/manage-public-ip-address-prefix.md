---
title: Criar, alterar ou excluir um prefixo de endereço IP público do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre prefixos de endereço IP público e como criá-los, alterá-los ou excluí-los. Consulte onde encontrar informações adicionais.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938651"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Criar, alterar ou excluir um prefixo de endereço IP público

Saiba mais sobre um prefixo de endereço IP público e como criar, alterar e excluir um. Um prefixo de endereço IP público é um intervalo contíguo de endereços com base no número de endereços IP públicos especificados. Os endereços são atribuídos à sua assinatura. Ao criar um recurso de endereço IP público, você pode atribuir um endereço IP público estático do prefixo e associar o endereço a máquinas virtuais, balanceadores de carga ou outros recursos, para habilitar a conectividade com a Internet. Se você não estiver familiarizado com prefixos de endereço IP público, consulte [Visão geral do prefixo de endereço IP público](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial exige a CLI do Azure versão 2.0.41 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

Prefixos do endereço IP público têm um encargo. Para detalhes, consulte o [preço](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Criar um prefixo de endereço IP público

1. No canto superior esquerdo do Portal, selecione **+ Criar um recurso**.
2. Insira o *prefixo IP público* na caixa *Pesquisar no Marketplace* . Quando **Prefixo de endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
3. Em **Prefixo de endereço IP público**, selecione **Criar**.
4. Insira ou selecione valores para as configurações a seguir, em **Criar prefixo de endereço IP público** e, em seguida, selecione **Criar**:

   |Configuração|Necessário?|Detalhes|
   |---|---|---|
   |Subscription|Sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você deseja associar o endereço IP público.|
   |Resource group|Sim|Pode existir no mesmo ou em outro [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que o recurso ao qual você deseja associar o endereço IP público.|
   |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado.|
   |Região|Sim|Deve existir no mesma [região](https://azure.microsoft.com/regions)que os endereços IP públicos que você atribuirá endereços do intervalo.|
   |Tamanho do prefixo|Sim| O tamanho do prefixo que você precisa. Um /28 ou 16 endereços IP é o padrão.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>Em regiões com zonas de disponibilidade, você pode usar os comandos do PowerShell ou da CLI para criar um prefixo de endereço IP público como: não zonal, associado a uma zona específica ou para usar a redundância de zona.  Para a versão de API 2020-08-01 ou posterior, se um parâmetro de zona não for fornecido, um prefixo de endereço IP público não zonal será criado. Para versões da API com mais de 2020-08-01, um prefixo de endereço IP público com redundância de zona é criado. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Criar um endereço IP público estático de um prefixo
Após criar um prefixo, você deverá criar endereços IP estáticos a partir do prefixo. Para fazer isso, siga as etapas abaixo.

1. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *prefixo de endereço IP público*. Quando **Prefixos de endereços IP públicos** aparecerem nos resultados da pesquisa, selecione-os.
2. Escolha o prefixo do qual você deseja criar IPs públicos.
3. Quando aparecer nos resultados da pesquisa, selecione-o e clique em **+Adicionar endereço IP** na seção Visão geral.
4. Insira ou selecione valores para as seguintes configurações em **Criar um endereço IP público**. Como um prefixo é para SKU Standard, IPv4 e estático, é necessário apenas fornecer as seguintes informações:

   |Configuração|Necessário?|Detalhes|
   |---|---|---|
   |Nome|Sim|O nome do endereço IP público deve ser exclusivo no grupo de recursos selecionado.|
   |Tempo limite de ociosidade (minutos)|Não|Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive. |
   |Rótulo do nome DNS|Não|Deve ser exclusivo na região do Azure na qual você cria o nome (em todas as assinaturas e em todos os clientes). O Azure registra automaticamente o nome e o endereço IP no DNS dele para que você possa se conectar a um recurso com o nome. O Azure acrescenta uma sub-rede padrão como *location.cloudapp.azure.com* (onde location é a localização selecionada) para o nome fornecido, para criar o nome DNS totalmente qualificado. Para obter mais informações, consulte [Usar DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Como alternativa, você pode usar os comandos da CLI e do PS abaixo com os parâmetros--Public-IP-prefix (CLI) e-PublicIpPrefix (PS) para criar um recurso de endereço IP público. 

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Exibir ou excluir um prefixo

1. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *prefixo de endereço IP público*. Quando **Prefixos de endereços IP públicos** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o nome do prefixo de endereço IP público que você quer exibir, alterar ou excluir da lista.
3. Conclua uma das seguintes opções, dependendo se você quer exibir, excluir ou alterar o prefixo de endereço IP público.
   - **Exibir**: a seção **Visão geral** mostra as principais configurações do prefixo de endereço IP público, como prefixo.
   - **Excluir**: para excluir o prefixo de endereço IP público, selecione **Excluir** na seção **Visão geral**. Se os endereços dentro do prefixo estiverem associados a recursos de endereço IP público, primeiro você deverá excluir os recursos de endereço IP público. Consulte [excluir um endereço IP público](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) para listar endereços IP públicos, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) para mostrar configurações; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) para atualizar; [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) para excluir|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) para recuperar um objeto de endereço IP público e exibir suas configurações, [set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) para atualizar as configurações; [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) para excluir|

## <a name="permissions"></a>Permissões

Para executar tarefas em prefixos de endereços IP públicos, a conta deve ser atribuída à função [Colaborador de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [Personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                                            | Nome                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Ler um prefixo de endereço IP público                                |
| Microsoft.Network/publicIPPrefixes/write                          | Criar ou atualizar um prefixo de endereço IP público                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Excluir um prefixo de endereço IP público                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Criar um endereço IP público de um prefixo |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre cenários e benefícios de usar um [prefixo de IP público](public-ip-address-prefix.md)
