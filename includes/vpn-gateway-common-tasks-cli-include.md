---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171780"
---
### <a name="to-view-local-network-gateways"></a>Para exibir os gateways de rede local

Para exibir uma lista de gateways de rede local, use o comando [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Para verificar os valores de chave compartilhados

Verifique se o valor de chave compartilhado é o mesmo valor usado para a configuração do dispositivo VPN. Caso contrário, execute a conexão novamente usando o valor do dispositivo ou atualize o dispositivo com o valor de retorno. Os valores devem ser correspondentes. Para exibir a chave compartilhada, use [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Para exibir o endereço IP público do gateway de VPN

Para localizar o endereço IP público do seu gateway de rede virtual, use o comando [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip). Para facilitar a leitura, a saída para este exemplo é formatada para exibir a lista de IPs públicos em formato de tabela.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
