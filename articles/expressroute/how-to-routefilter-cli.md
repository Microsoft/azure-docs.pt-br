---
title: 'Configurar filtros de rota para emparelhamento da Microsoft - ExpressRoute: CLI do Azure | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para o Emparelhamento da Microsoft usando a CLI do Azure
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: cfd9f4c52d3ddddd944186a833cba48e6ca76182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837854"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configurar filtros de rota para emparelhamento da Microsoft: CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [PowerShell do Azure](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma maneira de consumir um subconjunto de serviços com suporte por meio do emparelhamento da Microsoft. As etapas neste artigo lhe ajudam a configurar e gerenciar filtros de rota para circuitos de ExpressRoute.

Serviços do Dynamics 365 e serviços do Office 365, tais como o Exchange Online, o SharePoint Online e o Skype for Business, são acessíveis por meio do emparelhamento da Microsoft. Quando emparelhamento da Microsoft é configurado em um circuito de ExpressRoute, todos os prefixos relacionados a esses serviços são publicados por meio das sessões de BGP que são estabelecidas. Um valor de comunidade BGP é anexado a cada prefixo, pelo qual se pode identificar o serviço que é oferecido. Para obter uma lista dos valores de comunidade BGP e os serviços para os quais eles são mapeados, consulte [comunidades BGP](expressroute-routing.md#bgp).

Se você precisa de conectividade para todos os serviços, um grande número de prefixos é anunciado através do BGP. Isso aumenta consideravelmente o tamanho das tabelas de rotas mantidas por roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos pelo emparelhamento da Microsoft, você pode reduzir o tamanho de suas tabelas de rotas de duas maneiras. Você pode:

* Filtre prefixos indesejados aplicando filtros de rota nas comunidades do BGP. Essa é uma prática de rede padrão e é usada frequentemente em várias redes.

* Defina filtros de rota e aplique-as ao circuito de ExpressRoute. Um filtro de rota é um novo recurso que permite a você selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

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

* Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  * [Crie um circuito de ExpressRoute](howto-circuit-cli.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  * [Crie o emparelhamento da Microsoft](howto-routing-cli.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

* Você deve criar e configurar um filtro de rota.
  * Identifique os serviços que você deseja consumir por meio de emparelhamento da Microsoft
  * Identificar a lista de valores de comunidade BGP associados aos serviços
  * Criar uma regra para permitir a lista de prefixos que correspondem aos valores de comunidade BGP

* Você deve anexar o filtro de rota ao circuito de ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.

* Você deve ter um emparelhamento da Microsoft ativo. Siga as instruções em [Criar e modificar a configuração de emparelhamento](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura

Para iniciar sua configuração, entrar na sua conta do Azure. Se estiver usando a versão "Experimentar", você é conectado automaticamente e pode ignorar a etapa de logon. Use o exemplo a seguir para ajudar a conectar:

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

## <a name="prefixes"></a>Etapa 1: Obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores de comunidade BGP

Use o cmdlet a seguir para obter a lista de valores de comunidade BGP associados aos serviços acessíveis por meio do emparelhamento da Microsoft, junto com a lista de prefixos associados a eles:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que você deseja usar

Faça uma lista de valores de comunidade BGP que você deseja usar no filtro de rota. Por exemplo, o valor de comunidade BGP para serviços do Dynamics 365 é 12076:5040.

## <a name="filter"></a>Etapa 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Primeiro, crie o filtro de rota. O comando `az network route-filter create` só cria um recurso de filtro de rota. Depois de criar o recurso, você deve criar uma regra e anexá-la ao objeto de filtro de rota. Execute o comando a seguir para criar um recurso de filtro de rota:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Execute o comando a seguir para criar uma nova regra:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Etapa 3: Anexar o filtro de rota a um circuito de ExpressRoute

Execute o comando a seguir para anexar o filtro de rota ao circuito de ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, use o seguinte comando:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está anexado a um circuito, as atualizações à lista de comunidade BGP propagam automaticamente as alterações de anúncio de prefixo apropriadas por meio das sessões de BGP estabelecidas. Você pode atualizar a lista de comunidades BGP de seu filtro de rota usando o seguinte comando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Depois que um filtro de rota é desanexado do circuito de ExpressRoute, nenhum prefixo é anunciado através da sessão de BGP. Você pode desanexar um filtro de rota de um circuito de ExpressRoute usando o seguinte comando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Para excluir um filtro de rota

Você só pode excluir um filtro de rota se ele não está anexado a nenhum circuito. Verifique se o filtro de rota não está anexado a algum circuito antes de tentar excluí-lo. Você pode excluir um filtro de rota usando o seguinte comando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
