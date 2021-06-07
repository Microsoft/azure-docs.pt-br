---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devex-tag-azurecli
ms.openlocfilehash: 1a22c63eb01abc3775b2bf299d8464323e69c372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386849"
---
1. Inicie sessão na sua assinatura do Azure com o comando [az login](/cli/azure/) e siga as instruções na tela. Para obter mais informações sobre como conectar-se, confira [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Se tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

   ```azurecli
   az account list --all
   ```
3. Especifique a assinatura que você deseja usar.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
