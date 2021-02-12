---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359486"
---
* Você tem uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, confira o artigo [Início Rápido](../articles/virtual-network/quick-create-portal.md).

* Sua rede virtual não deverá ter gateways de rede virtual existentes. Caso a rede virtual já tenha gateways (VPN ou ExpressRoute), será necessário remover todos eles antes de continuar. Essa configuração requer que as redes virtuais se conectem somente ao gateway do hub da WAN Virtual.

* Um hub virtual é uma rede virtual criada e usada pela WAN Virtual. Ele é o núcleo da rede de WAN Virtual em uma região. Obtenha um intervalo de endereços IP para sua região de hub virtual. O intervalo de endereços especificado para o hub não poderá se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não poderá se sobrepor aos intervalos de endereços locais aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.
