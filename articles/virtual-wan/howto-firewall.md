---
title: Instalação do Firewall do Azure em um hub de WAN Virtual
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o Firewall do Azure em um hub de WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983674"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configuração do Firewall do Azure em um hub de WAN Virtual

Um **hub seguro** é um hub de WAN virtual do Azure com o Firewall do Azure. Este artigo orienta você pelas etapas para converter um hub de WAN Virtual em um hub seguro instalando o Firewall do Azure diretamente das páginas do portal de WAN Virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

As etapas neste artigo pressupõem que você já implantou uma WAN virtual com um ou mais hubs.

Para criar uma nova WAN virtual e um novo hub, siga as etapas nos artigos a seguir:

* [Criação de uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criação de um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Exibição de hubs virtuais

A página **Visão geral** da sua WAN virtual mostra uma lista de hubs virtuais e hubs seguros. A figura a seguir mostra uma WAN virtual sem hubs seguros.

[![Captura de tela mostra a página de visão geral de uma WAN virtual com uma lista de hubs virtuais.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Conversão para hub seguro

1. Na página **Visão geral** da sua WAN virtual, selecione o hub que você quer converter em um hub seguro. Na página do hub virtual, você verá duas opções para implantar o Firewall do Azure nesse hub. Selecione uma das opções.

   [![Captura de tela mostra a página de visão geral da WAN virtual, na qual você pode selecionar converter para o Hub seguro ou o Firewall do Azure.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Depois de selecionar uma das opções, a página **Converter para hub seguro** será exibida. Selecione um hub para converter e, em seguida, selecione **Avançar: Firewall do Azure** na parte inferior da página.

   [ ![selecionar hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Depois de concluir o fluxo de trabalho, selecione **Confirmar**.

   [![Captura de tela mostra o painel converter para o Hub seguro com confirmar selecionado.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Depois que o Hub tiver sido convertido em um hub seguro, você poderá vê-lo na página **Visão geral** da WAN virtual.

   [ ![exibir hub seguro](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Exibição dos recursos do hub

Na página **Visão geral** da WAN virtual, selecione o hub seguro. Na página do hub, você pode ver todos os recursos do hub virtual, incluindo o Firewall do Azure.

[ ![exibição dos recursos do hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Para exibir as configurações do Firewall do Azure no hub seguro, em **Segurança**, selecione **Configurações do hub virtual seguro**.
[ ![exibição das configurações do hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Definição de configurações adicionais

Para definir configurações adicionais do Firewall do Azure para o hub virtual, selecione o link para o **Gerenciador de Firewall do Azure**. Para obter informações sobre políticas de firewall, consulte o [Gerenciador de Firewall do Azure](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[ ![configurações adicionais](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Para retornar à página **Visão geral** do hub, navegue de volta clicando no caminho, conforme mostrado pela seta na figura a seguir.

[ ![voltar para visão geral](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
