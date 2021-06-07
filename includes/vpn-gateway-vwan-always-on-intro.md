---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91828903"
---
Um novo recurso do cliente de VPN do Windows 10, Always On, é a capacidade de manter uma conexão VPN. Com o Always On, o perfil de VPN ativo pode se conectar automaticamente e permanecer conectado com base em gatilhos, como entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.

Você pode usar gateways com o Windows 10 Always On para estabelecer túneis de usuário persistentes e túneis de dispositivo para o Azure.

Always On conexões VPN incluem um dos dois tipos de túneis:

* **Túnel de dispositivo**: conecta-se a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade de pré-logon e o gerenciamento de dispositivos usam um túnel de dispositivo.

* **Túnel do usuário**: conecta-se somente depois que os usuários entram no dispositivo. Usando túneis de usuário, você pode acessar os recursos da organização por meio de servidores VPN.

Os túneis de dispositivo e os túneis de usuário operam independentemente de seus perfis de VPN. Eles podem ser conectados ao mesmo tempo e podem usar diferentes métodos de autenticação e outras definições de configuração de VPN, conforme apropriado.
