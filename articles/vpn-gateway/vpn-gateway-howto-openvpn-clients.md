---
title: Como configurar clientes do OpenVPN para o Gateway de VPN do Azure | Microsoft Docs
description: Saiba como configurar o OpenVPN para o gateway de VPN do Azure para clientes do sistema operacional Windows, Linux e Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435772"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes do OpenVPN para o Gateway de VPN do Azure

Este artigo ajuda você a configurar clientes do **&reg; protocolo OpenVPN** .

## <a name="before-you-begin"></a>Antes de começar

Verifique se você concluiu as etapas para configurar o OpenVPN para seu gateway de VPN. Para obter detalhes, confira [Configurar o OpenVPN para Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximas etapas

Se você quiser que os clientes VPN possam acessar recursos em outra VNet, siga as instruções no artigo de [vnet para vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma conexão de vnet para vnet. Certifique-se de habilitar o BGP nos gateways e nas conexões, caso contrário, o tráfego não fluirá.

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
