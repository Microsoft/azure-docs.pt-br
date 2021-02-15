---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628911"
---
1. Na página da sua WAN Virtual, selecione **Configurações de VPN do usuário**.
1. Na página **Configurações de VPN de usuário**, selecione uma configuração e, em seguida, selecione **Baixar perfil de VPN de usuário da WAN virtual**. Ao baixar a configuração no nível de WAN, você obtém um perfil de VPN de usuário baseado no Gerenciador de Tráfego interno. Para obter mais informações sobre perfis globais ou perfis baseados em hub, confira [Perfis de hub](../articles/virtual-wan/global-hub-profile.md). Cenários de failover são simplificados com o perfil global.

   
   Se, por algum motivo, um hub não estiver disponível, o gerenciamento de tráfego interno fornecido pelo serviço garantirá a conectividade (por meio de um hub diferente) com os recursos do Azure para usuários de ponto a site. Você sempre pode baixar uma configuração de VPN específica do hub navegando até o hub. Em **VPN de Usuário (ponto a site)** , baixe o perfil de **VPN de Usuário** do hub virtual.
1. Na página **Baixar perfil de VPN de usuário da WAN virtual**, selecione o **Tipo de autenticação** e, em seguida, selecione **Gerar e baixar perfil**. O pacote de perfil será gerado e será feito o download de um arquivo zip contendo as configurações.
