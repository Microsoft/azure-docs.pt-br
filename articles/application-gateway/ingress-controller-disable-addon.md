---
title: Desabilitar e reabilitar o complemento do controlador de entrada do gateway de aplicativo para o cluster do serviço kubernetes do Azure
description: Este artigo fornece informações sobre como desabilitar e reabilitar o complemento AGIC para seu cluster AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807948"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Desabilitar e reabilitar o complemento AGIC para o cluster AKS
O AGIC (controlador de entrada do gateway de aplicativo) implantado como um complemento do AKS permite que você habilite e desabilite o complemento com uma linha no CLI do Azure. O ciclo de vida do gateway de aplicativo será diferente quando você desabilitar o complemento AGIC, dependendo de se o gateway de aplicativo foi criado pelo complemento AGIC ou se foi implantado separadamente do complemento AGIC. Você pode executar o mesmo comando para reabilitar o complemento AGIC se você o desabilitar, ou para habilitar o complemento AGIC usando um cluster AKS existente e um gateway de aplicativo.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Desabilitando o complemento AGIC com o gateway de aplicativo associado 
Se o complemento AGIC implantou automaticamente o gateway de aplicativo para você quando você configura o primeiro tudo, a desabilitação do complemento AGIC, por padrão, exclui o gateway de aplicativo com base em alguns critérios. Há dois critérios que o complemento AGIC procura para determinar se ele deve excluir o gateway de aplicativo associado ao desabilitá-lo:
- O gateway de aplicativo ao qual o complemento AGIC está associado está implantado no grupo de recursos MC_ * node? 
- O gateway de aplicativo ao qual o complemento AGIC está associado tem a marca "criado por: entrada-appgw"? A marca é usada por AGIC para determinar se o gateway de aplicativo foi implantado pelo complemento ou não. 

Se ambos os critérios forem atendidos, o complemento AGIC excluirá o gateway de aplicativo criado quando o complemento estiver desabilitado; no entanto, ele não excluirá o IP público ou a sub-rede na qual o gateway de aplicativo foi implantado com/. Se os primeiros critérios não forem atendidos, não importa se o gateway de aplicativo tem a marca "created-by: ingress-appgw"-desabilitar o complemento não excluirá o gateway de aplicativo. Da mesma forma, se o segundo critério não for atendido, ou seja, o gateway de aplicativo não tem essa marca, a desabilitação do complemento não excluirá o gateway de aplicativo no grupo de recursos MC_ * node. 

> [!TIP] 
> Se você não quiser que o gateway de aplicativo seja excluído ao desabilitar o complemento, mas ele atender aos dois critérios e, em seguida, remover a marca "criado por: entrada-appgw" para impedir que o complemento exclua seu gateway de aplicativo. 

Para desabilitar o complemento AGIC, execute o seguinte comando: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Habilitar o complemento AGIC no gateway de aplicativo existente e no cluster AKS
Se você desabilitar o complemento AGIC e precisar reabilitar o complemento, ou se quiser habilitar o complemento usando um gateway de aplicativo existente e um cluster AKS, execute o seguinte comando:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais detalhes sobre como habilitar o complemento AGIC usando um gateway de aplicativo existente e um cluster AKS, consulte [implantação de Brownfield do AGIC](tutorial-ingress-controller-add-on-existing.md).