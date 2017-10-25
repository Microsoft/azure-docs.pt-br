---
title: "Criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona usando a CLI do Azure | Microsoft Docs"
description: "Saiba como criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona usando a CLI do Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 769eb86af3e0506ddf03d1ec616d5a17b7e5f714
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Criar um Load Balancer Standard público com frontend com redundância de zona usando a CLI do Azure

Este artigo segue as etapas para a criação de um [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona usando um endereço IP Público Standard.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrar-se para a versão prévia das Zonas de Disponibilidade, do Load Balancer Standard e do IP Público Standard

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.17 ou superior.  Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[O SKU do Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) está atualmente em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use o [SKU do Load Balancer Básico](load-balancer-overview.md) geralmente disponível para os serviços de produção. 

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de selecionar uma opção de zona ou com redundância de zona para Endereço IP Público de front-end para o Load Balancer, você deve concluir as etapas em [Registrar-se para a versão prévia de zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).

Verifique se você instalou a versão mais recente da [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e está conectado a uma conta do Azure com [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#login).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o seguinte comando:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Criar um IP público Standard

Crie um IP Público Standard usando o seguinte comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Crie um Load Balancer Standard Público com o IP Público Standard que você criou na etapa anterior usando o seguinte comando:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Criar uma investigação do balanceador de carga na porta 80

Crie uma investigação de integridade do balanceador de carga usando o seguinte comando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Criar uma regra de Load Balancer para a porta 80

Crie uma regra do balanceador de carga usando o seguinte comando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Próximas etapas
- Saiba como [criar um IP público em uma zona de disponibilidade](../virtual-network/create-public-ip-availability-zone-cli.md)



