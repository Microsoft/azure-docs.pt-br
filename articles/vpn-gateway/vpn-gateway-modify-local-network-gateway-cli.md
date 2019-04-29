---
title: Modificar os prefixos de endereço IP do gateway de rede local e o endereço IP do Gateway de VPN | Azure | CLI | Microsoft Docs
description: Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local usando a CLI do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 20e8269f0ee7463dcff9a5f66e46ebd81cb3df84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652835"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as configurações de gateway de rede local usando a CLI do Azure

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações de gateway de rede local. Também é possível modificar essas configurações usando um método diferente, selecionando uma opção diferente da lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modificar os prefixos de endereço IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).

