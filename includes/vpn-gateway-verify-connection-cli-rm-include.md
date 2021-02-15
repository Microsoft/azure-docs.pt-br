---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217911"
---
Você pode verificar se a conexão foi bem-sucedida usando o comando [az network vpn-connection show](/cli/azure/network/vpn-connection). No exemplo, "--name" refere-se ao nome da conexão que você deseja testar. Quando a conexão ainda está sendo estabelecida, seu status de conexão mostra "Conectando". Quando a conexão for estabelecida, o status será alterado para "Conectado".

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
