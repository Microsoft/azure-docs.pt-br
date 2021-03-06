---
title: 'Tutorial: Configurar os filtros de rota para o emparelhamento da Microsoft – CLI do Azure'
description: Este tutorial descreve como configurar filtros de rota para o Emparelhamento da Microsoft usando a CLI do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975987"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Tutorial: Configurar filtros de rota para emparelhamento da Microsoft: CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [PowerShell do Azure](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma maneira de consumir um subconjunto de serviços com suporte por meio do emparelhamento da Microsoft. As etapas neste artigo lhe ajudam a configurar e gerenciar filtros de rota para circuitos de ExpressRoute.

Os serviços do Microsoft 365, tais como o Exchange Online, o SharePoint Online e o Skype for Business, são acessíveis por meio do emparelhamento da Microsoft. Quando emparelhamento da Microsoft é configurado em um circuito do ExpressRoute, todos os prefixos relacionados a esses serviços são publicados por meio das sessões de BGP que são estabelecidas. Um valor de comunidade BGP é anexado a cada prefixo, pelo qual se pode identificar o serviço que é oferecido. Para obter uma lista dos valores de comunidade BGP e os serviços para os quais eles são mapeados, consulte [comunidades BGP](expressroute-routing.md#bgp).

A conectividade com todos os serviços do Azure e Microsoft 365 faz um grande número de prefixos ser anunciado por meio do BGP. Esse grande número de prefixos aumenta consideravelmente o tamanho das tabelas de rotas mantidas por roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos pelo emparelhamento da Microsoft, você pode reduzir o tamanho de suas tabelas de rotas de duas maneiras. Você pode:

* Filtre prefixos indesejados aplicando filtros de rota nas comunidades do BGP. A filtragem de rota é uma prática de rede padrão e é usada frequentemente em muitas redes.

* Defina filtros de rota e aplique-as ao circuito de ExpressRoute. Um filtro de rota é um novo recurso que permite a você selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Obter valores de comunidade BGP.
> - Criar um filtro de rota e uma regra de filtro.
> - Associar o filtro de rota a um circuito do ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o emparelhamento da Microsoft é configurado no seu circuito do ExpressRoute, os roteadores do Microsoft Edge estabelecem um par de sessões de BGP com os roteadores do Edge por meio do seu provedor de conectividade. Nenhuma rota é anunciada para sua rede. Para habilitar os anúncios de rota para sua rede, você deve associar um filtro de rota.

Um filtro de rota permite identificar os serviços que você deseja consumir por meio de emparelhamento da Microsoft do seu circuito de ExpressRoute. Ele é essencialmente uma lista de permissões de todos os valores de comunidade do BGP. Depois que um recurso de filtro de rota é definido e anexado a um circuito do ExpressRoute, todos os prefixos que são mapeados para os valores de comunidade do BGP são anunciados para sua rede.

Para anexar filtros de rota aos serviços do Microsoft 365, você precisa ter autorização para consumir serviços do Microsoft 365 por meio do ExpressRoute. Se você não tiver autorização para consumir serviços do Microsoft 365 por meio do ExpressRoute, a operação de anexação de filtros de rota falhará. Para obter mais informações sobre o processo de autorização, confira o [Azure ExpressRoute para Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos de ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito.
> 

## <a name="prerequisites"></a>Pré-requisitos

Para se conectar aos serviços por meio do emparelhamento da Microsoft, você precisa concluir as seguintes etapas de configuração:

* Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  * [Crie um circuito do ExpressRoute](howto-circuit-cli.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  * [Crie o emparelhamento da Microsoft](howto-routing-cli.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura

Para iniciar sua configuração, entrar na sua conta do Azure. Se estiver usando a versão "Experimentar", você será entrará automaticamente e poderá ignorar a etapa de logon. Use o exemplo a seguir para ajudar a conectar:

```azurecli
az login
```

Verificar as assinaturas da conta.

```azurecli-interactive
az account list
```

Selecione a assinatura para a qual você deseja criar um circuito do ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Obter uma lista de prefixos e valores de comunidade BGP

1. Use o seguinte cmdlet para obter a lista de valores de comunidade do BGP associados aos serviços acessíveis por meio do emparelhamento da Microsoft:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Faça uma lista de valores de comunidade BGP que você deseja usar no filtro de rota.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela. O comando `az network route-filter create` cria somente um recurso de filtro de rota. Depois de criar o recurso, você deve criar uma regra e anexá-la ao objeto de filtro de rota.

1. Para criar um recurso de filtro de rota, execute o seguinte comando:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Para criar uma regra de filtro de rota, execute o seguinte comando:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Anexar o filtro de rota a um circuito de ExpressRoute

Execute o comando a seguir para anexar o filtro de rota ao circuito de ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, use o seguinte comando:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está anexado a um circuito, as atualizações à lista de comunidade do BGP propagam automaticamente as alterações de anúncio de prefixo apropriadas por meio das sessões BGP estabelecidas. Você pode atualizar a lista de comunidades BGP de seu filtro de rota usando o seguinte comando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Depois que um filtro de rota é desanexado do circuito de ExpressRoute, nenhum prefixo é anunciado através da sessão de BGP. Você pode desanexar um filtro de rota de um circuito de ExpressRoute usando o seguinte comando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Limpar recursos

Você só poderá excluir um filtro de rota se ele não estiver anexado a nenhum circuito. Verifique se o filtro de rota não está anexado a nenhum circuito antes de tentar excluí-lo. Você pode excluir um filtro de rota usando o seguinte comando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre exemplos de configuração de roteador, confira:

> [!div class="nextstepaction"]
> [Exemplos de configuração do roteador para configurar e gerenciar o roteamento](expressroute-config-samples-routing.md)
