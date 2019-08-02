---
title: Criar um Load Balancer com front-end de zona – CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer com front-end de zona usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 663567f6e3b078c1cb2afc60c3aaa9fcfb7af4dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275251"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Criar um Standard Load Balancer com front-end de zona usando a CLI do Azure

Este artigo segue as etapas para a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com front-end de zona. Ter um front-end de zona significa que qualquer fluxo de entrada ou saída é atendido por uma única zona em uma região. Você pode criar um balanceador de carga com um front-end zonal usando um endereço IP público padrão zonal em sua configuração de front-end. Para entender como as zonas de disponibilidade funcionam com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, certifique-se de que você instalou a versão mais recente da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e entrou em uma conta do Azure com [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> O suporte para Zonas de Disponibilidade está disponível para selecionar recursos e regiões do Azure e famílias de tamanho de VM. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o seguinte comando:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Criar um endereço IP público padrão

Crie um endereço IP Público padrão de zona usando o seguinte comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Crie um Standard Load Balancer Público com o IP Público Standard que você criou na etapa anterior usando o comando a seguir:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Criar uma investigação do balanceador de carga na porta 80

Crie uma investigação de integridade do balanceador de carga usando o seguinte comando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Criar uma regra de Load Balancer para a porta 80

Crie uma regra do balanceador de carga usando o seguinte comando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Load Balancer Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).



