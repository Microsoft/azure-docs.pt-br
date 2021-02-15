---
title: Integrar o NPS com a autenticação RADIUS de gateway de VPN para MFA
description: Descreve a integração da autenticação RADIUS do gateway do Azure com o servidor NPS para Autenticação Multifator.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 208e99f61694f5a81a98dbc649e2a6035f57891b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018266"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrar a autenticação RADIUS do gateway de VPN do Azure com o servidor NPS para Autenticação Multifator 

Este artigo descreve como integrar o NPS (Servidor de Políticas de Rede) com a autenticação de RADIUS do gateway de VPN do Azure para fornecer MFA (Autenticação Multifator) para conexões VPN de ponto a site. 

## <a name="prerequisite"></a>Pré-requisito

Para habilitar a MFA, os usuários devem estar no Azure AD (Azure Active Directory), que deve estar sincronizado do local ou do ambiente de nuvem. Além disso, o usuário já deve ter concluído o processo de registro automático para MFA.  Para obter mais informações, veja [Configurar minha conta para verificação em duas etapas](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Etapas detalhadas

### <a name="step-1-create-a-virtual-network-gateway"></a>Etapa 1: criar um gateway de rede virtual

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Na rede virtual que hospedará o gateway de rede virtual, selecione **Sub-redes** e, em seguida, selecione **Sub-rede de Gateway** para criar uma sub-rede. 

    ![A imagem sobre como adicionar a sub-rede de gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Crie um gateway de rede virtual especificando as seguintes configurações:

    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo de VPN**: selecione **baseado em rota**.
    - **SKU**: selecione um tipo de SKU de acordo com os seus requisitos.
    - **Rede virtual**: selecione a rede virtual em que você criou a sub-rede de gateway.

        ![A imagem sobre as configurações de gateway de rede virtual](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>Etapa 2 configurar o NPS para MFA do Azure AD

1. No servidor NPS, [Instale a extensão NPS para o Azure ad MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Abra o console do NPS, clique com o botão direito do mouse em **clientes RADIUS** e selecione **novo**. Crie o cliente RADIUS especificando as seguintes configurações:

    - **Nome Amigável**: digite um nome qualquer.
    - **Endereço (IP ou DNS)**: digite a sub-rede de gateway que você criou na Etapa 1.
    - **Segredo compartilhado**: digite qualquer chave secreta e lembre-se dela para uso posterior.

      ![A imagem sobre as configurações do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na guia **Avançado**, defina o nome do fornecedor para **RADIUS Standard** e certifique-se de que a caixa de seleção **Opções Adicionais** não está selecionada.

    ![A imagem sobre as configurações avançadas do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Vá para **políticas**  >  **políticas de rede**, clique duas vezes em **conexões com a política de servidor de acesso remoto e roteamento da Microsoft** , selecione **conceder acesso** e clique em **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Etapa 3: configurar o gateway de rede virtual

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Obtenha o gateway de rede virtual que você criou. Verifique se o tipo de gateway está definido como **VPN** e se o tipo de VPN é **baseado em rota**.
3. Clique em **configuração de ponto a site**  >  **Configurar agora** e especifique as seguintes configurações:

    - **Pool de endereços**: digite a sub-rede de gateway que você criou na etapa 1.
    - **Tipo de autenticação**: selecione **Autenticação RADIUS**.
    - **Endereço IP do servidor**: digite o endereço IP do servidor NPS.

      ![A imagem sobre configurações de ponto a site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Próximas etapas

- [Autenticação multifator do Azure AD](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integre sua infraestrutura de NPS existente com a autenticação multifator do Azure AD](../active-directory/authentication/howto-mfa-nps-extension.md)