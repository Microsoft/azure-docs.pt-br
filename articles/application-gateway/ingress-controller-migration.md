---
title: Como migrar do controlador de entrada do Aplicativo Azure gateway Helm para o complemento do AGIC
description: Este artigo fornece instruções sobre como migrar do AGIC implantado por meio do Helm para o AGIC implantado como um complemento do AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050827"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrar do AGIC Helm para o complemento AGIC 

Se você já tiver o AGIC implantado por meio de Helm, mas quiser migrar para o AGIC implantado como um complemento do AKS, as etapas a seguir ajudarão a orientá-lo durante o processo de migração. 

## <a name="prerequisites"></a>Pré-requisitos 
Antes de iniciar o processo de migração, há algumas coisas a serem verificadas. 
  - Você está usando qualquer recurso com AGIC Helm que [atualmente não tem suporte com o complemento AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Você está usando mais de uma implantação do AGIC Helm por cluster AKS? 
  - Você está usando várias implantações AGIC Helm para direcionar um gateway de aplicativo? 

Se você respondeu sim a qualquer uma das perguntas acima, o complemento AGIC não dará suporte ao seu caso de uso ainda, portanto, será melhor continuar usando o AGIC Helm enquanto isso. Caso contrário, continue com o processo de migração abaixo fora do horário comercial. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Localizar a ID de recurso do gateway de aplicativo que AGIC Helm está direcionando atualmente 
Navegue até o gateway de aplicativo que sua implantação do AGIC Helm está direcionando. Copie e salve a ID de recurso do gateway de aplicativo. Você precisará da ID do recurso em uma etapa posterior. A ID do recurso pode ser encontrada no portal, na guia Propriedades do seu gateway de aplicativo ou por meio de CLI do Azure. O exemplo a seguir salva a ID de recurso do gateway de aplicativo em *appgwId* para um gateway chamado *myApplicationGateway* no grupo de recursos *MyResource* Group.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Excluir AGIC Helm do seu cluster AKS
Por meio de CLI do Azure, exclua sua implantação do AGIC Helm do seu cluster. Você precisará excluir a implantação AGIC Helm primeiro antes de poder habilitar o complemento AGIC AKS. Observe que todas as alterações ocorridas no cluster AKS entre o momento da exclusão da implantação do AGIC Helm e a hora em que você habilita o complemento AGIC não serão refletidas no seu gateway de aplicativo e, portanto, esse processo de migração deve ser feito fora do horário comercial para minimizar o impacto. O gateway de aplicativo continuará a ter a última configuração aplicada por AGIC, de modo que as regras de roteamento existentes não serão afetadas. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Habilitar o complemento AGIC usando seu gateway de aplicativo existente 
Agora você pode habilitar o complemento AGIC em seu cluster AKS para direcionar seu gateway de aplicativo existente por meio de CLI do Azure ou Portal. Execute o seguinte comando CLI do Azure para habilitar o complemento AGIC em seu cluster AKS. O exemplo habilita o complemento em um cluster chamado *mycluster*, em um grupo de recursos chamado *MyResource* Group, usando a ID de recurso do gateway de aplicativo *appgwId* que salvamos acima na etapa anterior. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Como alternativa, você pode navegar para o cluster AKS no portal usando este [link](https://portal.azure.com/?feature.aksagic=true) e habilitar o complemento AGIC na guia rede do cluster. Selecione o gateway de aplicativo existente no menu suspenso ao escolher qual gateway de aplicativo o complemento deve ter como destino. 

![Portal do controlador de entrada do gateway de aplicativo](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Próximas etapas
- [**Solução de problemas do controlador de entrada do gateway de aplicativo**](ingress-controller-troubleshoot.md): guia de solução de problemas para AGIC 
- [**Anotações do controlador de entrada do gateway de aplicativo**](ingress-controller-annotations.md): lista de anotações em AGIC 