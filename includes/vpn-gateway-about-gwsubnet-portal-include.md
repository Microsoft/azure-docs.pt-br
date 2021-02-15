---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "71266531"
---
O gateway de rede virtual usa uma sub-rede específica denominada sub-rede de gateway. O gateway faz parte do intervalo de endereços IP de rede virtual que você especifica ao configurar sua rede virtual. Contém os endereços IP usados pelos recursos e serviços do gateway de rede virtual. 


Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. O número de endereços IP necessários depende da configuração do gateway VPN que você deseja criar. Algumas configurações exigem mais endereços IP do que outras. Recomendamos que você crie uma sub-rede de gateways que usa um /27 ou /28.


Se você vir um erro que especifica que o espaço de endereço se sobrepõe a uma sub-rede, ou que a sub-rede não está contida no espaço de endereço para sua rede virtual, verifique o intervalo de endereços de VNet. Talvez você não tenha endereços IP suficientes disponíveis no intervalo de endereços que você criou para sua rede virtual. Por exemplo, se a sua sub-rede padrão envolver todo o intervalo de endereços, não restará qualquer endereço IP para criar outras sub-redes. Você pode ajustar suas sub-redes no espaço de endereço existente a fim de liberar endereços IP, ou especificar um intervalo de endereços adicional e criar ali a sub-rede de gateway.