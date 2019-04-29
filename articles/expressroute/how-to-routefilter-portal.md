---
title: 'Configurar os filtros de rota para o emparelhamento da Microsoft: Azure ExpressRoute – portal | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para o Emparelhamento da Microsoft usando o portal do Azure.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839166"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurar os filtros de rota para o emparelhamento da Microsoft: Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [PowerShell do Azure](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma maneira de consumir um subconjunto de serviços com suporte por meio do emparelhamento da Microsoft. As etapas neste artigo lhe ajudam a configurar e gerenciar filtros de rota para circuitos de ExpressRoute.

Os serviços do Dynamics 365 e os serviços do Office 365, tais como o Exchange Online, o SharePoint Online e o Skype for Business, bem como os serviços do Azure como o armazenamento e o BD SQL estão acessíveis por meio do emparelhamento da Microsoft. Quando emparelhamento da Microsoft é configurado em um circuito de ExpressRoute, todos os prefixos relacionados a esses serviços são publicados por meio das sessões de BGP que são estabelecidas. Um valor de comunidade BGP é anexado a cada prefixo, pelo qual se pode identificar o serviço que é oferecido. Para obter uma lista dos valores de comunidade BGP e os serviços para os quais eles são mapeados, consulte [comunidades BGP](expressroute-routing.md#bgp).

Se você precisa de conectividade para todos os serviços, um grande número de prefixos é anunciado através do BGP. Isso aumenta consideravelmente o tamanho das tabelas de rotas mantidas por roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos pelo emparelhamento da Microsoft, você pode reduzir o tamanho de suas tabelas de rotas de duas maneiras. Você pode:

- Filtre prefixos indesejados aplicando filtros de rota nas comunidades do BGP. Essa é uma prática de rede padrão e é usada frequentemente em várias redes.

- Defina filtros de rota e aplique-as ao circuito de ExpressRoute. Um filtro de rota é um novo recurso que permite a você selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre filtros de rota

Quando o emparelhamento da Microsoft é configurado no seu circuito de ExpressRoute, os roteadores de borda da Microsoft estabelecem um par de sessões de BGP com os roteadores de borda (seus ou de seu provedor de conectividade). Nenhuma rota é anunciada para sua rede. Para habilitar os anúncios de rota para sua rede, você deve associar um filtro de rota.

Um filtro de rota permite identificar os serviços que você deseja consumir por meio de emparelhamento da Microsoft do seu circuito de ExpressRoute. Ele é essencialmente uma lista de permissões de todos os valores de comunidade do BGP. Depois que um recurso de filtro de rota é definido e anexado a um circuito de ExpressRoute, todos os prefixos que são mapeados para os valores de comunidade do BGP são anunciados para sua rede.

Para que seja possível anexar filtros de rota que incluam serviços do Office 365, você deve ter autorização para consumir serviços do Office 365 por meio do ExpressRoute. Se você não está autorizado a consumir serviços do Office 365 por meio do ExpressRoute, a operação de anexação de filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Conectividade com serviços Dynamics 365 NÃO exigem nenhuma autorização anterior.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos de ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de roteamento não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito.
> 
> 

### <a name="workflow"></a>Fluxo de trabalho

Para poder se conectar aos serviços por meio do emparelhamento da Microsoft, você deve concluir as etapas de configuração a seguir:

- Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  - [Crie um circuito de ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  - [Crie o emparelhamento da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

-  Você deve criar e configurar um filtro de rota.
    - Identifique os serviços que você deseja consumir por meio de emparelhamento da Microsoft
    - Identificar a lista de valores de comunidade BGP associados aos serviços
    - Criar uma regra para permitir a lista de prefixos que correspondem aos valores de comunidade BGP

-  Você deve anexar o filtro de rota ao circuito de ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você satisfaz os seguintes critérios:

 - Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

 - Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.

 - Você deve ter um emparelhamento da Microsoft ativo. Siga as instruções em [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Etapa 1: Obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores de comunidade BGP

Os valores de comunidade BGP associados aos serviços acessíveis por meio de emparelhamento da Microsoft está disponível na página [Requisitos de roteamento do ExpressRoute](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que você deseja usar

Faça uma lista de valores de comunidade BGP que você deseja usar no filtro de rota. Por exemplo, o valor de comunidade BGP para serviços do Dynamics 365 é 12076:5040.

## <a name="filter"></a>Etapa 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota
Você pode criar um circuito de rota selecionando a opção de criar um novo recurso. Clique em **Criar um recurso** > **Rede** > **RouteFilter**, conforme mostrado na imagem a seguir:

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Você deve colocar o filtro de rota em um grupo de recursos. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Você pode adicionar e atualizar regras selecionando a guia de regra de gerenciar para o filtro de rota.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Você pode selecionar os serviços aos quais deseja se conectar na lista suspensa e salvar a regra quando terminar.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Etapa 3: Anexar o filtro de rota a um circuito de ExpressRoute

Você pode anexar o filtro de rota a um circuito selecionando o botão "Adicionar circuito" e selecionando o circuito de ExpressRoute na lista suspensa.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se o provedor de conectividade configura o emparelhamento do seu circuito do ExpressRoute atualize o circuito da folha do circuito do ExpressRoute antes de selecionar o botão "Adicionar circuito".

![Criar um filtro de rota](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Você pode exibir as propriedades de um filtro de rota quando abre o recurso no portal.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Você pode atualizar a lista de valores de comunidade BGP anexada a um circuito ao selecionar o botão "Gerenciar regra".


![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Para desanexar um circuito do filtro de rota, clique com o botão direito do mouse no circuito e clique em “Desassociar”.

![Criar um filtro de rota](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Para excluir um filtro de rota

Você pode excluir um filtro de rota selecionando o botão de exclusão. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).