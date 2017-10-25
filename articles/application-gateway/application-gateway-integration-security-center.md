---
title: "Integração de Gateway de Aplicativo do com a Central de segurança do Azure | Microsoft Docs"
description: "Esta página fornece informações sobre como o Gateway de Aplicativo se integra à Central de segurança do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: abed4095bcacac4ca3995acfaafae2f16d5f1532
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Visão geral da integração entre o Gateway de Aplicativo e a Central de Segurança do Azure

Saiba como o Gateway de Aplicativo e a Central de segurança ajudam a proteger os recursos do aplicativo Web. O firewall do aplicativo Web do Gateway de aplicativo (WAF) integra-se com a [Central de segurança](../security-center/security-center-intro.md) para fornecer uma visualização perfeita para evitar, detectar e reagir às ameaças a aplicativos Web desprotegidos no seu ambiente.

## <a name="overview"></a>Visão geral

O WAF do Gateway de Aplicativo é uma recomendação na Central de segurança para proteger aplicativos Web contra explorações e vulnerabilidades. Recursos da Web que não estão protegidos por WAF são exibidos na central de segurança como recomendações de severidade alta. Recomendações para firewalls de aplicativo Web são mostradas na página **Visão geral** em **Aplicativos**.

![integração com a central de segurança][1]

Clicando em uma recomendação sobre o firewall do aplicativo Web abre uma nova folha mostrando os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicionar o firewall do aplicativo Web a um recurso existente

Navegue até **Mais serviços** > **Segurança + Identidade** > **Central de Segurança** e na folha **Central de Segurança - Visão geral**, clique em **Aplicativos**. Na folha **Central de Segurança - Aplicativos**, a tabela contém uma lista de aplicativos que a Central de segurança detectou na sua assinatura.

![aplicativos Web][3]

Ao clicar em um aplicativo Web com um problema crítico, você obtém a folha **Integridade da segurança do aplicativo**. Na imagem abaixo, está o aplicativo Web que não está protegido por um firewall do aplicativo Web. 

![recursos Web não protegidos][2]

Clique em **Adicionar um firewall do aplicativo Web** em **Recomendações** para abrir a folha **Adicionar um Firewall do aplicativo Web**.

Se você não tiver um Gateway de aplicativo existente ou quiser criar um novo, clique em **Criar novo** e na folha **Criar um novo Firewall do aplicativo Web**, clique em **Microsoft - Gateway de Aplicativo**. Isso leva você para as etapas para criar um gateway de aplicativo. Neste ponto, seu aplicativo Web é adicionado como um recurso protegido, agora a Central de segurança controla o que esse recurso está protegido por um firewall do aplicativo Web. Isso não o adiciona como um membro do pool de back-end.

Se você tiver um gateway de aplicativo existente, é possível escolhê-lo em **Usar solução existente**

![folha adicionar firewall do aplicativo Web][4]

Adicionar que um aplicativo Web a um gateway de aplicativo por meio da Central de segurança não adiciona o recurso como um membro do pool de back-end, isso deve ser feito no recurso de gateway de aplicativo diretamente.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicionar um recurso a um firewall do aplicativo Web existente

Navegue até **Mais serviços** > **Segurança + Identidade** > **Central de Segurança** e na folha **Central de Segurança - Visão geral**, clique em **Soluções de parceiros**. Gateways de aplicativo com reconhecimento da Central de segurança existentes na folha **Soluções de parceiros**.

![soluções de parceiros][7]

Clique em **Vincular aplicativo** para abrir a folha **Vincular aplicativos**, aqui você terá as opções para selecionar os aplicativos existentes. Escolha os aplicativos para serem protegidos e clique em **OK**. Isso não adiciona o aplicativo Web ao pool de back-end do gateway de aplicativo. Isso define os recursos como um recurso protegido para que a Central de segurança possa controlá-lo. Para adicionar o recurso como um membro do pool de back-end, isso deve ser feito no gateway de aplicativo, na folha atual, você pode clicar em **Console de solução** para ser levado ao recurso de gateway de aplicativo onde você pode adicionar o aplicativo Web ao pool de back-end.

![aplicativos de soluções de parceiros][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

A Central de segurança controla os aplicativos adicionados a um gateway de aplicativo como um recurso protegido.  Ela monitora a integridade desse recurso e garante que ele seja protegido por um gateway de aplicativo. A próxima etapa é adicionar o IP privado, o IP público ou a NIC de sua máquina virtual para o pool de back-end do gateway de aplicativo. Até que isso seja feito, uma recomendação adicional de **Finalizar a proteção do aplicativo** é mostrada até que o recurso seja adicionado.

![folha adicionar firewall do aplicativo Web][5]

## <a name="security-alerts"></a>Alertas de segurança

Na Central de segurança, navegue até **DETECÇÃO** > **Alertas de segurança**.  Aqui você encontra alertas do WAF para seus gateways de aplicativo. Os alertas são divididos pela regra do WAF.

![alertas de segurança][8]

Clicar em uma regra fornecerá uma lista de alertas para essa regra específica do WAF. Cada alerta mostra detalhes adicionais sobre a descoberta. Os detalhes fornecem um link para o gateway de aplicativo.
 
![detalhes do alerta][9]

## <a name="next-steps"></a>Próximas etapas

Para saber como ativar o firewall de aplicativo Web em um Gateway de Aplicativo existente, visite [Criar ou atualizar um Gateway de Aplicativo do Azure com o firewall de aplicativo Web](application-gateway-web-application-firewall-portal.md#add-web-application-firewall-to-an-existing-application-gateway)

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png