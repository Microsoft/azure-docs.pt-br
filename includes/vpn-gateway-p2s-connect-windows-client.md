---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041506"
---
>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows do qual você está se conectando.
>

1. Para se conectar à sua VNet, no computador cliente, navegue até configurações de VPN e localize a conexão VPN que você criou. Ele tem o mesmo nome que a sua rede virtual. Selecione **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Selecione **continuar** para usar privilégios elevados.

1. Na página de status **Conexão**, selecione **Conectar** para iniciar a conexão. Se você vir uma tela **Selecionar Certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e, em seguida, selecione **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Conectar-se de um computador com Windows":::

1. A conexão é estabelecida.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Conectar-se de um computador a um diagrama de conexão de ponto a site da VNet do Azure":::
