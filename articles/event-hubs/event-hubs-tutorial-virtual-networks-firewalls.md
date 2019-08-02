---
title: Tutorial – Habilitar a integração de redes virtuais e firewalls em Hubs de Eventos | Microsoft Docs
description: Neste tutorial, você aprenderá como integrar os Hubs de Eventos com redes virtuais e firewalls para permitir o acesso seguro.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 0f7c7e348c154aab1deb10273346a5395599b745
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605851"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Tutorial: Habilitar a integração de redes virtuais e firewalls no namespace dos Hubs de Eventos

[Os pontos de extremidade de serviço de VNet (rede virtual)](../virtual-network/virtual-network-service-endpoints-overview.md) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Os firewalls permitem que você limite o acesso ao namespace de Hubs de Eventos de endereços IP específicos (ou intervalos de endereços IP)

Este tutorial mostra como integrar o pontos de extremidade de serviço de redes virtuais e configurar os firewalls (filtragem de IP) com seu namespace de Hubs de Eventos do Azure existente, usando o portal.

Neste tutorial, você aprenderá:
> [!div class="checklist"]
> * Como integrar os pontos de extremidade de serviço de redes virtuais com seu namespace de Hubs de Eventos.
> * Como configurar o Firewall (filtragem de IP) com seu namespace de Hubs de Eventos.

>[!WARNING]
> Implementar a integração de redes virtuais pode impedir que outros serviços do Azure interajam com Hubs de Eventos.
>
> Integrações internas não são compatíveis quando as Redes Virtuais estão habilitadas.
> Cenários comuns do Azure que não funcionam com redes virtuais –
> * Diagnóstico do Azure e Registro em Log
> * Stream Analytics do Azure
> * Integração da Grade de Eventos
> * É necessário que as funções e aplicativos Web estejam em uma rede virtual.
> * Rotas do Hub IoT
> * IoT Device Explorer


> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Aproveitaremos um namespace de Hubs de Eventos existente, portanto, verifique se você tem um namespace de Hubs de Eventos disponível. Se não tem, veja [este tutorial](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Primeiro, acesse o [portal do Azure][Azure portal] e entre nele usando sua assinatura do Azure.

## <a name="select-event-hubs-namespace"></a>Selecionar um namespace de Hubs de Eventos

Para fins deste tutorial, criamos um namespace de Hubs de Eventos e navegaremos até ele.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navegue até a experiência de firewalls e redes virtuais

Use o menu de navegação no painel à esquerda no portal para escolher a opção **'Firewalls e Redes Virtuais'**.

  ![Navegar até o menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Na primeira vez que você visitar esta página, o botão de opção **Todas as Redes** precisa ser selecionado. Isso implica que o namespace de Hubs de Eventos permite todas as conexões de entrada.

## <a name="add-virtual-network-service-endpoint"></a>Adicionar ponto de extremidade de serviço de rede virtual

Para limitar o acesso, você precisa integrar o ponto de extremidade de serviço de rede virtual a esse namespace de Hubs de Eventos.

1. Clique no botão de opção **Redes Selecionadas** na parte superior da página para habilitar o restante da página com as opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na seção Rede Virtual da página, selecione a opção para ***+ Adicionar rede virtual existente***. Isso fará com que apareça o painel deslizante que permite que você selecione uma rede virtual já criada.
  ![adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Selecione a rede virtual na lista e selecione a sub-rede.
   ![selecionar sub-rede](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Você precisa habilitar o ponto de extremidade de serviço antes de adicionar a rede virtual à lista. Se o ponto de extremidade de serviço não estiver habilitado, o portal solicitará que você o habilite.
  ![selecionar a sub-rede e habilitar o ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Se não for possível habilitar o ponto de extremidade de serviço, você poderá ignorar o ponto de extremidade de serviço de rede virtual ausente usando o modelo ARM. Essa funcionalidade não está disponível no portal.

5. Depois de habilitar o ponto de extremidade de serviço na sub-rede selecionada, você poderá adicioná-lo à lista de redes virtuais permitidas.
  ![adicionar sub-rede depois de habilitar o ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Continue a pressionar o botão **Salvar** na faixa de opções superior para salvar a configuração de rede virtual no serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.

## <a name="add-firewall-for-specified-ip"></a>Adicionar um firewall ao IP especificado

Usando as regras de Firewall, é possível limitar o acesso ao namespace de Hubs de Eventos para um limitado intervalo de endereços IP ou um endereço IP específico.

1. Clique no botão de opção **Redes Selecionadas** na parte superior da página para habilitar o restante da página com as opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na seção **Firewall**, na grade ***Intervalo de Endereços***, você pode adicionar um ou muitos endereços IP específicos ou intervalos de endereços IP.
  ![adicionar endereços IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Depois de adicionar vários endereços IP (ou intervalos de endereços IP), pressione **Salvar** na faixa de opções superior para garantir que a configuração seja salva no lado do serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.
  ![adicionar endereços ip e clicar em salvar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Adicionar seu endereço IP atual para às regras de firewall

1. Você também pode adicionar seu endereço IP atual rapidamente, marcando a caixa de seleção ***Adicionar seu endereço IP do cliente (SEU ENDEREÇO IP ATUAL)*** logo acima da grade ***Intervalo de Endereços***.
  ![adicionando o endereço IP atual](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Após você ter adicionado seu endereço IP atual às regras de firewall, pressione **Salvar** na faixa de opções superior para garantir que a configuração seja salva no lado do serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.
  ![adicionar endereço IP atual e clicar em salvar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusão

Neste tutorial, você integrou regras de firewall e pontos de extremidade de serviço de rede virtual com um namespace de Hubs de Eventos existente. Você aprendeu:
> [!div class="checklist"]
> * Como integrar os pontos de extremidade de serviço de redes virtuais com seu namespace de Hubs de Eventos.
> * Como configurar o Firewall (filtragem de IP) com seu namespace de Hubs de Eventos.


[Azure portal]: https://portal.azure.com/
