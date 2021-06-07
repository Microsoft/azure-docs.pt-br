---
title: Substituição de sub-rede do Gerenciador de tráfego do Azure usando CLI do Azure | Microsoft Docs
description: Este artigo o ajudará a entender como a substituição de sub-rede do Gerenciador de tráfego pode ser usada para substituir o método de roteamento de um perfil do Gerenciador de tráfego para direcionar o tráfego para um ponto de extremidade com base no endereço IP do usuário final por meio do intervalo de IP predefinido para mapeamentos de ponto de extremidade.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211321"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Substituição de sub-rede do Gerenciador de tráfego usando CLI do Azure

A substituição de sub-rede do Gerenciador de tráfego permite alterar o método de roteamento de um perfil.  A adição de uma substituição direcionará o tráfego com base no endereço IP do usuário final com um intervalo de IP predefinido para o mapeamento de ponto de extremidade. 

## <a name="how-subnet-override-works"></a>Como a substituição de sub-rede funciona

Quando as substituições de sub-rede são adicionadas a um perfil do Gerenciador de tráfego, o Gerenciador de tráfego primeiro verificará se há uma substituição de sub-rede para o endereço IP do usuário final. Se um for encontrado, a consulta DNS do usuário será direcionada para o ponto de extremidade correspondente.  Se um mapeamento não for encontrado, o Gerenciador de tráfego fará o fallback para o método de roteamento original do perfil. 

Os intervalos de endereços IP podem ser especificados como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a cada ponto de extremidade devem ser exclusivos para esse ponto de extremidade. Qualquer sobreposição de intervalos de IP entre diferentes pontos de extremidade fará com que o perfil seja rejeitado pelo Gerenciador de tráfego.

Há dois tipos de perfis de roteamento que dão suporte a substituições de sub-rede:

* **Geográfico** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará a consulta para o ponto de extremidade, independentemente da integridade do ponto de extremidade.
* **Desempenho** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará apenas o tráfego para o ponto de extremidade se ele estiver íntegro.  O Gerenciador de tráfego fará o fallback para o heurística de roteamento de desempenho se o ponto de extremidade de substituição de sub-rede não estiver íntegro.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma substituição de sub-rede do Gerenciador de tráfego

Para criar uma substituição de sub-rede do Gerenciador de tráfego, você pode usar CLI do Azure para adicionar as sub-redes para a substituição para o ponto de extremidade do Gerenciador de tráfego.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Atualize o ponto de extremidade do Gerenciador de tráfego com a substituição de sub-rede.
Use CLI do Azure para atualizar seu ponto de extremidade com [AZ Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

Você pode remover os intervalos de endereços IP executando a [atualização de ponto de extremidade AZ Network Traffic-Manager](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) com a opção **--Remove** .

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

Saiba mais sobre o [método de roteamento de tráfego de sub-rede](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)