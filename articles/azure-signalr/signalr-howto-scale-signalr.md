---
title: Dimensionar uma instância do serviço de Signaler do Azure
description: Saiba como dimensionar uma instância do serviço de Signaler do Azure para adicionar ou reduzir a capacidade, por meio de portal do Azure ou CLI do Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595760"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Como dimensionar uma instância do serviço de Signaler do Azure?
Este artigo mostra como dimensionar sua instância do serviço de Signaler do Azure. Há dois cenários para dimensionar, escalar verticalmente e escalar horizontalmente.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais unidades, conexões, mensagens e muito mais. Você escala verticalmente alterando o tipo de preço de gratuito para padrão.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de unidades do signalr. Você pode escalar horizontalmente até 100 unidades. Há opções de unidade limitadas a serem selecionadas para o dimensionamento: 1, 2, 5, 10, 20, 50 e 100 unidades para uma única instância do serviço Signalr.

As configurações de escala levam alguns minutos para serem aplicadas. Em casos raros, pode levar cerca de 30 minutos para ser aplicado. Eles não exigem que você altere seu código ou reimplante o aplicativo do servidor.

Para obter informações sobre os preços e as capacidades do serviço Signalr individual, consulte [detalhes de preços do serviço de signaler do Azure](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Alterar o serviço de Signalr da camada **gratuita** para a camada **Standard** ou vice-versa, o IP do serviço público será alterado e geralmente levará de 30-60 minutos para propagar a alteração para servidores DNS em toda a Internet. Seu serviço pode estar inacessível antes de o DNS ser atualizado. Geralmente, não é recomendável alterar seu tipo de preço com muita frequência.


## <a name="scale-on-azure-portal"></a>Dimensionar em portal do Azure

1. No seu navegador, abra o [portal do Azure](https://portal.azure.com).

2. Na página de serviço do Signalr, no menu à esquerda, selecione **escala**.
   
3. Escolha seu tipo de preço e, em seguida, clique em **selecionar**. Defina a contagem de unidades para a camada **Standard** .
   
    ![Dimensionar no portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Clique em **Salvar**.

## <a name="scale-using-azure-cli"></a>Dimensionar usando a CLI do Azure

Esse script cria um novo recurso de serviço de Signalr da camada **gratuita** e um novo grupo de recursos e o dimensiona para a camada **Standard** . 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Anote o nome real gerado para o novo grupo de recursos. Você usará esse nome de grupo de recursos quando quiser excluir todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, como mensagens e conexões incluídas para cada tipo de preço, consulte [detalhes de preços do serviço de sinalização](https://azure.microsoft.com/pricing/details/signalr-service/).

Para obter uma tabela de limites de serviço, cotas e restrições em cada camada, consulte [limites de serviço de sinalização](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como dimensionar a instância de serviço de sinal único.

Vários pontos de extremidade também têm suporte para dimensionamento, fragmentação e cenários entre regiões.

> [!div class="nextstepaction"]
> [dimensionar o serviço do Signalr com várias instâncias](./signalr-howto-scale-multi-instances.md)
