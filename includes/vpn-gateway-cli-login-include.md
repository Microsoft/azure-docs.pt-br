---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 2ffd8b42bf43658ba488f4c1c92bb7af5b88339e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755602"
---
Inicie sessão na sua assinatura do Azure com o comando [az login](/cli/azure/) e siga as instruções na tela. Para obter mais informações sobre como conectar-se, confira [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Se tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

```azurecli
az account list --all
```

Especifique a assinatura que você deseja usar.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
