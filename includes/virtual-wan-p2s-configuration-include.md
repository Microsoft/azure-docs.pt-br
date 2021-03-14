---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048243"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Navegue até **Todos os recursos** e selecione a WAN criada. Em seguida, selecione **Configurações de VPN do usuário** no menu à esquerda.
1. Na página **Configurações de VPN do usuário**, selecione **+Criar configuração de VPN do usuário** no início da página para abrir a página **Criar configuração de VPN do usuário**.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Captura de tela da página de configurações de VPN do usuário.":::

1. Na guia **Noções básicas**, em **Detalhes da instância**, insira o **Nome** que deseja atribuir à configuração de VPN.
1. Em **Tipo de túnel**, selecione o tipo de túnel desejado na lista suspensa. As opções de tipo de túnel são: **VPN IKEv2**, **OpenVPN** e **OpenVpn e IkeV2**.
1. Use as etapas a seguir que correspondam ao tipo de túnel selecionado. Depois que todos os valores forem especificados, clique em **Examinar + Criar** e em **Criar** para criar a configuração.

   **VPN IKEv2**

   * **Requisitos:** quando você seleciona o tipo de túnel **IKEv2**, aparece uma mensagem que pede a escolha de um método de autenticação. Para IKEv2, você pode especificar apenas um método de autenticação. Você pode escolher o Certificado do Azure, o Azure Active Directory ou autenticação baseada em RADIUS.

   * **Parâmetros de IPsec personalizados:** para personalizar os parâmetros das fases 1 e 2 do IKE, alterne o botão de IPsec para **Personalizar** e selecione os valores dos parâmetros. Para obter mais informações sobre parâmetros personalizáveis, confira o artigo [IPsec personalizado](../articles/virtual-wan/point-to-site-ipsec.md).

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Captura de tela do botão IPsec alternado para Personalizar.":::

   * **Autenticação:** navegue até o mecanismo de autenticação que deseja usar clicando em **Avançar** no final da página para passar para o método de autenticação ou clicando na guia apropriada no início da página. Alterne o botão para **Sim** para selecionar o método.

     O exemplo mostra a seleção da autenticação RADIUS. Para a autenticação baseada em RADIUS, você pode informar um endereço IP secundário do servidor RADIUS e o segredo do servidor.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Captura de tela do IKE.":::

   **OpenVPN**

   * **Requisitos:** quando você seleciona o tipo de túnel **OpenVPN**, aparece uma mensagem que pede a escolha de um mecanismo de autenticação. Se OpenVPN for selecionado como o tipo de túnel, você poderá especificar vários métodos de autenticação. Você pode escolher qualquer subconjunto de Certificado do Azure, Azure Active Directory ou autenticação baseada em RADIUS. Para a autenticação baseada em RADIUS, você pode informar um endereço IP secundário do servidor RADIUS e o segredo do servidor.

   * **Autenticação:** navegue até os métodos de autenticação que deseja usar clicando em **Avançar** no final da página para passar para o método de autenticação ou clicando na guia apropriada no início da página.
   Para cada método que desejar selecionar, alterne o botão para **Sim** e insira os valores apropriados.

     O exemplo mostra a seleção do Azure Active Directory.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Captura de tela da página OpenVPN.":::
