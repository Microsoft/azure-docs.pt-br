---
title: Solucionar Problemas de erros comuns de implantação
titleSuffix: Azure Load Balancer
description: Descreve como resolver erros comuns ao implantar Azure Load Balancers
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695547"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Solucionar problemas de erros comuns de implantação do Azure com o Azure Load Balancer

Este artigo descreve alguns erros comuns de implantação do Azure Load Balancer e fornece informações para resolver os erros. Se você estiver procurando informações sobre um código de erro e se essas informações não forem fornecidas neste artigo, informe-nos. Na parte inferior desta página, você pode deixar comentários. Os comentários são acompanhados com problemas do GitHub.

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Detalhes e mitigação |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| O SKU do IP público e o SKU do Load Balancer devem coincidir. Verifique se os SKUs de IP Público e do Azure Load Balancer correspondem. SKU Standard é recomendado para cargas de trabalho de produção. Saiba mais sobre as [diferenças entre SKUs](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Os conjuntos de dimensionamento de máquinas virtuais padrão para Load Balancers básicos quando o SKU não é especificado ou implantado sem IPs públicos Standard. Reimplante o conjunto de dimensionamento de máquinas virtuais com IPs públicos Standard nas instâncias individuais para garantir que Standard Load Balancer esteja selecionado ou simplesmente selecione um Standard LB ao implantar o conjunto de dimensionamento de máquinas virtuais do portal do Azure. |
|MaxAvailabilitySetsInLoadBalancerReached | O pool de back-end de um Load Balancer pode conter um máximo de 150 conjuntos de disponibilidade. Se você não tiver conjuntos de disponibilidade definidos explicitamente para suas VMs no pool de back-end, cada VM entrará em seu próprio conjunto de disponibilidade. Assim, a implantação de 150 VMs autônomas significa que ela teria 150 Conjuntos de disponibilidade, atingindo assim o limite. Você pode implantar um conjunto de disponibilidade e adicionar outras VMs a ele como uma solução alternativa. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Para o balanceador de carga de SKU básico, a interface de rede e o balanceador de carga precisam estar no mesmo conjunto de disponibilidade. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Você não pode ter mais de uma regra em um determinado tipo de balanceador de carga (interno, público) com a mesma porta de back-end e o protocolo referenciados pelo mesmo conjunto de dimensionamento de máquinas virtuais. Atualize sua regra para alterar essa criação de regra duplicada. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Você não pode ter mais de uma regra em um determinado tipo de balanceador de carga (interno, público) com a mesma porta de back-end e o protocolo referenciados pelo mesmo conjunto de dimensionamento de máquinas virtuais. Atualize os parâmetros de regra para alterar essa criação de regra duplicada. |
|AnotherInternalLoadBalancerExists| Você pode ter apenas um Load Balancer do tipo referência interna do mesmo conjunto de VMs/interfaces de rede no back-end do Load Balancer. Atualize a implantação para garantir que você está criando apenas um Load Balancer do mesmo tipo. |
|CannotUseInactiveHealthProbe| Você não pode ter uma investigação que não seja usada por nenhuma regra configurada para a integridade do conjunto de dimensionamento de máquinas virtuais. Verifique se a investigação configurada está sendo usada ativamente. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Você não pode ter vários Load Balancers do mesmo tipo (interno, público). Você pode ter um máximo de um Load Balancer interno e um público. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Não há suporte para Load Balancer básico para conjuntos de dimensionamento de máquinas virtuais de grupos de vários posicionamentos ou conjunto de dimensionamento de máquinas virtuais de zona de disponibilidade cruzada. Em vez disso, use o Standard Load Balancer. |
|MarketplacePurchaseEligibilityFailed | Use para a conta administrativa correta para habilitar as compras devido à assinatura ser uma assinatura de EA. Leia mais [aqui](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Se seu load balancer estiver em um estado de falha, siga estas etapas para recuperá-lo:<ol><li>Vá para https://resources.azure.com e entre com suas credenciais do portal do Azure.</li><li>Selecione **Leitura/Gravação**.</li><li>À esquerda, expanda **Assinaturas** e, em seguida, expanda a Assinatura com o Load Balancer que será atualizado.</li><li>Expanda **ResourceGroups** e, em seguida, expanda o grupo de recursos com o Load Balancer que será atualizado.</li><li>Selecione **Microsoft.Network** > **LoadBalancers** e, em seguida, selecione o Load Balancer que será atualizado, **LoadBalancer_1**.</li><li>Na página de exibição do **LoadBalancer_1**, selecione **GET** > **Editar**.</li><li>Atualize o valor **ProvisioningState** de **Com falha** para **Bem-sucedido**.</li><li>Selecione **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Próximas etapas

* Consulte a [tabela de comparação do SKU](./skus.md) do Azure Load Balancer
* Saiba mais sobre os [limites do Azure Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)