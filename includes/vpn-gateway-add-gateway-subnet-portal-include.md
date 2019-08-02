---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171769"
---
1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da página VNet, clique em **Sub-redes** para expandir a página Sub-redes.
3. Na página **Sub-redes**, clique em **+Sub-rede de gateway** no topo para abrir a página **Adicionar sub-rede**.

   ![Adicionar a sub-rede de gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Adicionar a sub-rede de gateway")
  
4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. O valor GatewaySubnet é necessário para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração.

   ![Adição da sub-rede de gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Adição da sub-rede de gateway")
  
5. Para criar a sub-rede, clique em **OK** na parte inferior da página.
