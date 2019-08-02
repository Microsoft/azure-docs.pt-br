---
title: Aumento de limite de rede | Microsoft Docs
description: Aumento de limite de rede
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297287"
---
# <a name="networking-limit-increase"></a>Aumento de limite de rede

Para exibir seu uso de rede atual e a cota, você pode visitar o **usos + cota** folha no portal do Azure. Você também pode usar o uso [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) ou o [API de uso de rede](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) para exibir o uso de rede e os limites.

Você pode solicitar um aumento via **ajuda + suporte** folha ou o **usos + cota** folha no portal.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Solicitar aumento de cota de rede no nível de assinatura usando o **ajuda + suporte** folha

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha de 'Ajuda + suporte' do Azure disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **ajuda + suporte**.

    ![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Nova solicitação de suporte**. 

    ![Nova solicitação de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. No menu suspenso tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

    ![Lista suspensa de tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a assinatura que precisa de uma cota maior.

    ![Selecione a assinatura newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **Networking** na **tipo de cota** lista suspensa. 

    ![Selecione o tipo de cota](./media/networking-quota-request/select-quota-type-network.png)

6. Na **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando **fornecem detalhes**.

    ![Forneça detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No **detalhes da cota** painel, selecione o modelo de implantação, um local e os recursos que você deseja solicitar um aumento.

    ![Detalhes da cota DM](./media/networking-quota-request/quota-details-network.png)

8.  Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o recurso na lista suspensa recurso ou clique no ícone "x" de descarte. Depois de inserir a cota desejada para cada recurso, clique em **salvar e continuar** no painel de detalhes da cota para continuar com a criação de solicitação de suporte.

    ![Novos limites](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Solicitar aumento de cota de rede no nível de assinatura usando **usos + cota** folha

Siga as instruções abaixo usar para criar uma solicitação de suporte por meio do Azure "uso + cota" folha disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **Assinaturas**.

    ![Assinaturas](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a assinatura que precisa de uma cota maior.

    ![Escolha a assinatura](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **Uso + cotas**

    ![Selecione uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **Solicitar Aumento**.

    ![Solicitar Aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Siga as etapas, começando na etapa 3 das *aumento de cota de solicitação de rede no nível de assinatura* seção usando o **ajuda + suporte** seção da folha

## <a name="about-networking-limits"></a>Sobre limites de rede

Para saber mais sobre limites de rede, consulte o [seção rede](../azure-subscription-service-limits.md#networking-limits) da página limites ou nossas perguntas frequentes sobre limites de rede