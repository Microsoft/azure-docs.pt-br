---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171826"
---
Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Se você já tiver uma rede virtual à qual deseja se conectar, verifique se nenhuma das sub-redes da rede local se sobrepõe às redes virtuais que serão conectadas. Sua rede virtual não requer uma sub-rede de gateway e não pode ter gateways de rede virtual. Se você não tiver uma rede virtual, poderá criar uma usando as etapas neste artigo.
* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual, e o intervalo de endereços especificado para a região de hub não pode se sobrepor a alguma rede virtual à qual você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes.
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.