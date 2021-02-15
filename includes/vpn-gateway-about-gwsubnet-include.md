---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a734963ced9daefda2b7b6f4a52fd9ef437eddc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "71269274"
---
O gateway de rede virtual usa uma sub-rede específica denominada sub-rede de gateway. O gateway faz parte do intervalo de endereços IP de rede virtual que você especifica ao configurar sua rede virtual. Contém os endereços IP usados pelos recursos e serviços do gateway de rede virtual. A sub-rede deve ser denominada "GatewaySubnet" para que o Azure implante os recursos de gateway. Você não pode especificar uma sub-rede diferente para implantação dos recursos de gateway. Se você não tiver uma sub-rede denominada "GatewaySubnet", quando você criar o gateway de VPN, ele falhará.

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. O número de endereços IP necessários depende da configuração do gateway VPN que você deseja criar. Algumas configurações exigem mais endereços IP do que outras. Recomendamos que você crie uma sub-rede de gateways que usa um /27 ou /28.

Se você vir um erro que especifica que o espaço de endereço se sobrepõe a uma sub-rede, ou que a sub-rede não está contida no espaço de endereço para sua rede virtual, verifique o intervalo de endereços de VNet. Talvez você não tenha endereços IP suficientes disponíveis no intervalo de endereços que você criou para sua rede virtual. Por exemplo, se a sua sub-rede padrão envolver todo o intervalo de endereços, não restará qualquer endereço IP para criar outras sub-redes. Você pode ajustar suas sub-redes no espaço de endereço existente a fim de liberar endereços IP, ou especificar um intervalo de endereços adicional e criar ali a sub-rede de gateway.