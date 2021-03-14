---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627678"
---
1. Localize o WAN Virtual que você criou. Na página WAN Virtual, na seção **Conectividade**, selecione **Hubs**.
2. Na página **Hubs**, selecione **+Novo Hub** para abrir a página **Criar hub virtual**.

    ![A captura de tela mostra o painel Criar hub virtual com a guia Informações Básicas selecionada.](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. Na página **Criar Hub Virtual**, na guia **Básico**, preencha os seguintes campos:

   * **Região** (anteriormente conhecida como Localização)
   * **Nome**
   * **Espaço de endereço privado do hub** – O espaço de endereço mínimo é /24 para criar um hub. Se você usar um valor no intervalo de /25 a /32, será produzido um erro durante a criação. Você não precisa planejar explicitamente o espaço de endereço de sub-rede para os serviços no hub virtual. Como a WAN Virtual do Azure é um serviço gerenciado, ela cria as sub-redes apropriadas no hub virtual para os diferentes gateways/serviços (por exemplo, gateways de VPN, gateways do ExpressRoute, gateways de ponto a site de VPN do usuário, firewall, roteamento etc.).
4. Selecione **Avançar: Site a site**.

    ![A captura de tela mostra o painel Criar hub virtual com Site a site selecionado.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a site")

5. Na guia **Site a site**, preencha os seguintes campos:

   * Selecione **Sim** para criar uma VPN site a site.
   * O campo de Número do AS não pode ser editado.
   * Selecione o valor de **Unidades de escala de gateway** no menu suspenso. A unidade de escala permite escolher a taxa de transferência agregada do gateway de VPN que está sendo criado no hub virtual ao qual conectar os sites. Se você escolher que 1 unidade de escala = 500 Mbps, isso significará que duas instâncias de redundância serão criadas, cada uma com uma taxa de transferência máxima de 500 Mbps. Por exemplo, se você tiver cinco branches, cada um com 10 Mbps, precisará de um total de 50 Mbps na cabeceira de rede. O planejamento da capacidade total do gateway de VPN do Azure deve ser realizado depois de avaliar a capacidade necessária para dar suporte ao número de branches para o Hub.
6. Selecione **Examinar + Criar** para validar.
7. Selecione **Criar** para criar o hub. Depois de 30 minutos, **Atualize** para exibir o hub na página **Hubs**. Selecione **Ir para o recurso** para navegar até o recurso.
