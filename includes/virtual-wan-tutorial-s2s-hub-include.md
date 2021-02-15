---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 64470b42efeea49b7c778d6dffd88465b8445e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606488"
---
1. Localize o WAN Virtual que você criou. Na página WAN virtual, na seção **Conectividade**, selecione **Hubs**.
2. Na página Hubs, selecione **+Novo Hub** para abrir a página **Criar Hub Virtual**.

    ![A captura de tela mostra o painel Criar hub virtual com a guia Informações Básicas selecionada.](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. Na página **Criar Hub Virtual**, na guia **Básico**, preencha os seguintes campos:

    **Detalhes do projeto**

   * Região (anteriormente conhecida como Local)
   * Nome
   * Espaço de endereço privado do hub. O espaço de endereço mínimo é /24 para criar um hub, o que significa que qualquer intervalo entre /25 e /32 produzirá um erro durante a criação. Como um serviço gerenciado pela Microsoft, a WAN Virtual do Azure cria as sub-redes apropriadas no hub virtual para os diferentes gateways/serviços (por exemplo, gateways de VPN, gateways do ExpressRoute, gateways de ponto a site/VPN de usuário, firewall, roteamento etc.). Não há necessidade de o usuário planejar explicitamente o espaço de endereço de sub-rede para os serviços no hub virtual, pois a Microsoft faz isso como parte do serviço.
4. Selecione **Avançar: Site a site**.

    ![A captura de tela mostra o painel Criar hub virtual com Site a site selecionado.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a site")

5. Na guia **Site a site**, preencha os seguintes campos:

   * Selecione **Sim** para criar uma VPN site a site.
   * No momento, o campo Número do sistema autônomo não pode ser editado no hub virtual.
   * Selecione o valor de **Unidades de escala de gateway** no menu suspenso. A unidade de escala permite escolher a taxa de transferência agregada do gateway de VPN que está sendo criado no hub virtual ao qual conectar os sites. Se você escolher que 1 unidade de escala = 500 Mbps, isso significará que duas instâncias de redundância serão criadas, cada uma com uma taxa de transferência máxima de 500 Mbps. Por exemplo, se você tiver cinco branches, cada um com 10 Mbps, precisará de um total de 50 Mbps na cabeceira de rede. O planejamento da capacidade total do gateway de VPN do Azure deve ser realizado depois de avaliar a capacidade necessária para dar suporte ao número de branches para o Hub.
6. Selecione **Examinar + Criar** para validar.
7. Selecione **Criar** para criar o hub. Depois de 30 minutos, **Atualize** para exibir o hub na página **Hubs**. Selecione **Ir para o recurso** para navegar até o recurso.
