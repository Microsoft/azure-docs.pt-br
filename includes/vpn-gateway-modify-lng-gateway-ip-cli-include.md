---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026493"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar o 'gatewayIpAddress' para um gateway de rede local

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. O endereço IP do gateway pode ser alterado sem remover uma conexão de gateway de VPN existente (se houver uma). Para modificar o endereço IP do gateway, substitua os valores 'Site2' e 'TestRG1' pelos seus próprios valores usando o comando [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verifique se o endereço IP está correto na saída:

```
"gatewayIpAddress": "23.99.222.170",
```