---
title: 'Tutorial: Configurar filtros de rota para o emparelhamento da Microsoft – portal do Azure'
description: Esse tutorial descreve como configurar filtros de rota para o emparelhamento da Microsoft usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109129"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Tutorial: Configurar filtros de rota para o emparelhamento da Microsoft usando o portal do Azure

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

- Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

- Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  - [Crie um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  - [Crie o emparelhamento da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Obter uma lista de prefixos e valores de comunidade BGP

### <a name="get-a-list-of-bgp-community-values"></a>Obter uma lista de valores de comunidade BGP

Os valores de comunidade BGP associados aos serviços acessíveis por meio de emparelhamento da Microsoft está disponível na página [Requisitos de roteamento do ExpressRoute](expressroute-routing.md).

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Faça uma lista dos valores que você deseja usar

Faça uma lista de [valores de comunidade BGP](expressroute-routing.md#bgp) que você deseja usar no filtro de rota. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

1. Selecione **Criar um recurso**. Em seguida, pesquise *Filtro de rota*, conforme mostrado na seguinte imagem:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Captura de tela que mostra a página Filtro de rota":::

1. Coloque o filtro de rota em um grupo de recursos. Verifique se a localização é a mesma que o circuito do ExpressRoute. Selecione **Examinar + criar** e depois **Criar**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Captura de tela que mostra a página Criar filtro de rota com valores de exemplo inseridos":::

### <a name="create-a-filter-rule"></a>Criar uma regra de filtro

1. Para adicionar e atualizar regras, selecione a guia gerenciar regra para o filtro de rota.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Captura de tela que mostra a página Visão geral com a ação Gerenciar regra realçada":::

1. Selecione os serviços aos quais deseja se conectar na lista suspensa e salve a regra quando terminar.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Captura de tela que mostra a janela Gerenciar regra com os serviços selecionados na lista suspensa Comunidades de serviço permitidas":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Anexar o filtro de rota a um circuito de ExpressRoute

Anexe o filtro de rota a um circuito selecionando o botão **+ Adicionar Circuito** e selecionando o circuito do ExpressRoute na lista suspensa.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Captura de tela que mostra a página Visão geral com a ação Adicionar circuito selecionada":::

Se o provedor de conectividade configura o emparelhamento do seu circuito do ExpressRoute, atualize o circuito da página do circuito do ExpressRoute antes de selecionar o botão **+ Adicionar Circuito**.

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Captura de tela que mostra a página Visão geral com a ação Atualizar selecionada.":::

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Você pode exibir as propriedades de um filtro de rota quando abre o recurso no portal.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Captura de tela mostrando a página Visão geral":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

1. Você pode atualizar a lista de valores de comunidade BGP anexada a um circuito selecionando o botão **Gerenciar regra**.

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Atualizar Filtros de rota com a ação Gerenciar regra":::

1. Selecione as comunidades de serviço que você deseja e, em seguida, selecione **Salvar**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Captura de tela que mostra a janela Gerenciar regra com os serviços selecionados":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Para desanexar um circuito do filtro de rota, clique com o botão direito do mouse no circuito e selecione **Desassociar**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Captura de tela que mostra a página Visão geral com a ação Desassociar realçada":::


## <a name="clean-up-resources"></a>Limpar os recursos

Você pode excluir um filtro de rota selecionando o botão **Excluir**. Verifique se o Filtro de rota não está associado a nenhum circuito antes de fazer isso.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Excluir um filtro de rota":::

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre exemplos de configuração de roteador, confira:

> [!div class="nextstepaction"]
> [Exemplos de configuração do roteador para configurar e gerenciar o roteamento](expressroute-config-samples-routing.md)
